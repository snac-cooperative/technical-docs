SNAC2 Algorithms to Match
---------------------------

The process to match persons (`match_persons`)

1. For each unprocessed record:  
    1. Try to find an exact string match of the name in already matched records and Cheshire's VIAF index (`match_person_exact`).
    2. If no record group and no VIAF ID found, search for name in VIAF index using ngrams (`match_person_ngram_viaf`).
        * If match quality is null, then create new record group not matching VIAF (no VIAF record found).
        * If match quality is above 0 and less than threshhold, then create new group flagging as maybe VIAF match (VIAF record found, but might not be correct).
        * If match quality is above 0 and above threshhold, then create new group with this VIAF ID (close enough VIAF record found).
        * If match quality is -1, create a new group for this record (NO VIAF ID candidate).
    3. If no record group, but VIAF ID found, create a new record group linking to this VIAF ID.
    4. If a record group with VIAF ID was found, then match into this record group.

The process to match Corporations (`match_corporate`)

1. For each unprocessed record:  
    1. Try to find an exact string match in VIAF index (`match_exact`).
        * If no record group and no VIAF ID found, try Keyword viaf name search (`match_corp_keyword_viaf`).
            * Try keyword match (`viaf_match_keyword`). If match quality is greater than 0, then look up group in Postgres by VIAF ID from the keyword match and return that group.  Else, returns nothing and match quality = -1.
            * If match quality is above accept threshold, then use the record group returned by the keyword search.
            * If match quality is above fuzzy match threshold, create new group for this record, noting this VIAF ID as a maybe match.
        * If no record group but a VIAF ID found, create a new group for this record.
    2. Add this record to the group found/created.

The process to match Families (`match_families`)

1. For each unprocessed record:  
    1. Look up the record group by searching postgres for the normalized name of the family.
    2. If the record has no group found, create one.

SNAC2 Algorithms to Merge
--------------------------

Merging Records

1. For each record group (matched set of records):  *I believe this includes the MaybeSame records, which are "maybe" links to VIAF identities.  The "maybe" VIAF IDs are stored in a separate Postgres table from the record groups.*
    * Create the merged record with the group name (the first element's normalized name).
    * Store the merged record (in Postges).
    * Get canonical ID.
        * Either ARK temporary, ARK permanent (`-r` parameter), or no id (`-n` parameter).
    * Store the merged record (in Postgres).

Assembling Records

1. For each merged record (in Postgres):
    * Create the combined record as follows.
        * Combine each CPF record's name, type, sources, exist dates, occupations, local descriptions, functions, resource relations, and biog hists.
        * For each CPF record's relations, if a merged record containing the other side of the relation is found and it has a canonical ID, add the `xlink:href` link and remove the `descriptiveNote` elements from the relation.  The merged relations are kept.
        * Write all merged data into a string in EAC-CPF XML form as the combined record.
    * If the merged record has an ARK id and the combined record can be parsed by Python's XML parser.
        * Grab the canonical ARK id, stripping `ark:/` and replace `/` with `-`.
        * Write the combined record to file at `merged_directory/ARKID.xml` in utf-8.
        * Note the merged record in Postres as processed.
    * If the merged record has no ARK id or can't be parsed by Python's XML parser, an exception is thrown and assembling stops.

Reassign IDs from File

1. Read all canonical IDs from the file given after `-f` argument into an array.
2. For each merged record with no canonical id assigned, assign a canonical id from the array and write back to Postgres.


Uses of Cheshire
====================

### Summary

Cheshire, via CheshirePy, is used in only a few places in the snac2 match and postprocess code.  Specifcially, in the following ways:

1. Exact string searching: a normalized name is queried into Cheshire for VIAF records with exact string matches in the mainHeadings->data->text tags. Note, only the first 1 result is used.  This is a fast indexing.
2. Ngram searching: a normalized name is queried into Cheshire for VIAF records with ngrams matches in the mainHeadings->data->text tags.  Note, only the first 10 results are used.  *Based on ngrams Cheshire queries for Geonames, there appears to be an ordering issue on results.  The top 10 might not have the intended match.*
3. Keyword searching: a normalized name is queried into Cheshire for VIAF records with keyword matchings in the mainHeadings->data->text tags.  Note, only the first 10 results are used.
4. Exact VIAF ID searching: requesting a VIAF record with a given ID (in the viafID tag).
5. Keyword ID Number searching: querying an id number for VIAF records with that id number in the sources->source tags.

*Note: the normalized names mentioned above come from the first name entry in the EAC record, and are cleaned with the following code (when being inserted into the Postgres database).  This normalization returns lower case with no punctuation and with accents replaced with non-accented characters.  (We need to update the comments in code for `str_remove_punc`, and should not need to replace the accents because of unicode character matching).*

```
return compress_spaces(strip_accents(str_remove_punc(str_clean_and_lowercase(s), replace_with=" ")))
```

### Matching (match.py)

1. Exact string match VIAF search of a record into a group: (`match_exact`)
    * A candidate group is found by calling `group.get_by_name(record.name_norm)`, where the latter is the normalized name of the record to match into the current groups.
        * This searches the groups in Postgres and looks for a group with this normalized name. 
    * If a group is found AND group has a VIAF record associated, check the dates and match (if equal).
    * Else, look up the `record.name_norm` as an exact match into **Cheshire**'s VIAF exact name index (exact string matches only).
        * Found in VIAF's mainHeadings->data->text tags.
        * If record dates match to viaf record dates (within a 10-year margin for birth/death dates), then this VIAF entry matches.
            * If a record group has this VIAF ID, then match in to that group.
            * If not, then create a new group.
        * If dates don't match, return no match found.
        * If VIAF doesn't have dates, return no match found.
        * If no VIAF entry, return no match found.
    * <span style="color:blue">*We could probably clean up the if/else logic here: the variable `match` is set to `true`, and never changed.  When something fails to match, the literal `false` is returned.  Also, there is a return statement at the end of the viaf_records section that is only reachable by passing the first two inner if statements.*</span>
2. Exact string match VIAF search of a person record to a group: (`match_person_exact`, line 231)
    * If we're not forcing a rematch,
        * Look up `record.norm_name` in PersonGroup models, to see if it exists in the current set of matches.
        * If found, check for the dates for each record in the group.
            * Find largest score between person record dates and each record in the group's dates.  These scores do not have to come from the same record.  (1pt active dates match, 3pts birth/death exact match, 2pts birth/death date within 10 years, 0pts otherwise. *This only checks whether the years match*)
            * If the normalized name is longer than 10 chars and either of the scores in date is greater than the fail threshold, then this is a match (return this match).
    * Look up `record.name_norm` as an exact match into **Cheshire**'s VIAF exact name index (exact string matches only).  Search includes whether or not looking for spirit (if `(spirit)` was in the normalized record string).
        * Found in VIAF's mainHeadings->data->text tags.
        * If record dates match to viaf record dates (within a 10-year margin for birth/death dates), then this VIAF entry matches.
            * If a record group has this VIAF ID, then match in to that group.
            * If not, then create a new group.
        * If dates don't match, return no match found.
        * If VIAF doesn't have dates, return no match found.
        * If no VIAF entry, return no match found.
3. Find VIAF person by ngrams: (`match_person_ngram_viaf`, line 283)
    * Calls through to `viaf_match_ngram`, line 385 to get match and match quality.
        * Get only top 10 results from **Cheshire**'s ngram search.
            * Found in VIAF's mainHeadings->data->text tags.
        * For each of the 10 top results:
            * If existence dates don't match (failure), return no match found.
            * If existence dates match exactly (requires only that birth and death years match exactly), return this candidate VIAF record as match.
            * If dates aren't exact but don't fail (partial/fuzzy match: active dates match exactly or birth and death years are within 10-year margin).
                * If (person) compute:
                    * Use name parser (python [nameparser](https://pypi.python.org/pypi/nameparser) package) to separate string into parts (match and query).
                        * If there is only one name each, check for exact string match and accept if so.
                        * If there are multiple parts and the last names and first names exact string match, then
                            * If middle names exist and they exact string match, accept.
                            * If middle names exist and don't match, reject.
                            * If no middle names exist, accept.
                            * If one of the two middle names is an initial, if first characters match, accept at 0.99 (not 1.0) as fuzzy match.
                            * Else, compute the Jaro Winkler distance of "First Last" and return the threshold value if the JW distance is above the threshold (or JW distance if below).
                        * If there are multiple parts, but only first initials exist instead of full first name, and the last names, first initials, middle names (no initials) all match, then accept.
                    * Otherwise, do Jaro Winkler distance between the strings, return 0.00001 below the accept threshold if the JW distance is above the threshold (or JW distance if below).
                * If (corporation) compute Jaro Winkler Distance and accept/return if above threshold.
                * If (other) compute simple relative length and accept/return if above threshold.
    * If match quality is greater than 0:
        * If record group found with that VIAF ID, then match into that group.
        * If not, then create a new group.
    * If match quality less than or equal to 0, return no match found.
4. Find VIAF record by keywords: (`viaf_match_keyword`, line 514)
    * Clean up normalized name for record of which to find match.
    * Query **Cheshire**'s VIAF name index for normalized name.
        * Found in VIAF's mainHeadings->data->text tags.
        * Returns 10 candidates by default.
        * For each candidate, compute [Jaro-Winkler Distance](http://en.wikipedia.org/wiki/Jaro–Winkler_distance) between candidate and normalized name.  If distance is above fuzzy match threshold, return that candidate.  (*This stops at the first match above the threshold*).


### Postprocessing (postprocess.py)

1. Look up VIAF records by VIAF ID, which happens in two places.
    * To reload VIAF record for a record group (matched group), query **Cheshire**'s `viafid` index for the group's `viaf_id`.
        * If found, it repaces the record stored with the group with the new version from Cheshire's result.
        * If not found, the ID numbers of each source from the existing viaf_record stored with the group are queried in **Cheshire**'s `idnumber` index to find matching VIAF records.
            * Depending on the results found, this record group may be merged into another group that has a matching VIAF record found in this manner.
