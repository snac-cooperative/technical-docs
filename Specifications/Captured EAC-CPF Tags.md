## Parsed Tags

The following tags and attribute structure is currently being parsed by SNAC's EAC-CPF parser:

<ul>
    <li><code>eac-cpf</code>
    <ul>
        <li><code>control</code>
        <ul>
            <li><code>recordId</code></li>
            <li><code>otherRecordId</code>
            <ul>
                <li><code>@localType</code></li>
            </ul></li>
            <li><code>maintenanceStatus</code></li>
            <li><code>maintenanceAgency</code>
            <ul>
                <li><code>agencyName</code></li>
            </ul></li>
            <li><code>languageDeclaration</code>
            <ul>
                <li><code>language</code>
                <ul>
                    <li><code>@languageCode</code></li>
                    <li><code>@scriptCode</code></li>
                </ul></li>
            </ul></li>
            <li><code>maintenanceHistory</code>
            <ul>
                <li><code>maintenanceEvent</code>
                <ul>
                    <li><code>eventType</code></li>
                    <li><code>eventDateTime</code>
                    <ul>
                        <li><code>@standardDateTime</code></li>
                    </ul></li>
                    <li><code>agentType</code></li>
                    <li><code>agent</code></li>
                    <li><code>eventDescription</code></li>
                </ul></li>
            </ul></li>
            <li><code>conventionDeclaration</code></li>
            <li><code>sources</code>
            <ul>
                <li><code>source</code>
                <ul>
                    <li><code>@type</code></li>
                    <li><code>@href</code></li>
                </ul></li>
            </ul></li>
        </ul></li>
        <li><code>cpfDescription</code>
        <ul>
            <li><code>identity</code>
            <ul>
                <li><code>entityType</code></li>
                <li><code>nameEntry</code>
                <ul>
                    <li><code>@preferenceScore</code></li>
                    <li><code>@lang</code></li>
                    <li><code>@scriptCode</code></li>
                    <li><code>part</code></li>
                    <li><code>alternativeForm</code></li>
                    <li><code>authorizedForm</code></li>
                    <li><code>useDates</code>
                    <ul>
                        <li><code>dateRange</code>
                        <ul>
                            <li><code>fromDate</code>
                            <ul>
                                <li><code>@standardDate</code></li>
                                <li><code>@localType</code></li>
                                <li><code>@notBefore</code></li>
                                <li><code>@notAfter</code></li>
                            </ul></li>
                            <li><code>toDate</code>
                            <ul>
                                <li><code>@standardDate</code></li>
                                <li><code>@localType</code></li>
                                <li><code>@notBefore</code></li>
                                <li><code>@notAfter</code></li>
                            </ul></li>
                        </ul></li>
                        <li><code>date</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                </ul></li>
            </ul></li>
            <li><code>description</code>
            <ul>
                <li><code>existDates</code>
                <ul>
                    <li><code>dateSet</code>
                    <ul>
                        <li><code>dateRange</code>
                        <ul>
                            <li><code>fromDate</code>
                            <ul>
                                <li><code>@standardDate</code></li>
                                <li><code>@localType</code></li>
                                <li><code>@notBefore</code></li>
                                <li><code>@notAfter</code></li>
                            </ul></li>
                            <li><code>toDate</code>
                            <ul>
                                <li><code>@standardDate</code></li>
                                <li><code>@localType</code></li>
                                <li><code>@notBefore</code></li>
                                <li><code>@notAfter</code></li>
                            </ul></li>
                        </ul></li>
                        <li><code>date</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                    <li><code>dateRange</code>
                    <ul>
                        <li><code>fromDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                        <li><code>toDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                    <li><code>date</code>
                    <ul>
                        <li><code>@standardDate</code></li>
                        <li><code>@localType</code></li>
                        <li><code>@notBefore</code></li>
                        <li><code>@notAfter</code></li>
                    </ul></li>
                    <li><code>descriptiveNote</code></li>
                </ul></li>
                <li><code>place</code>
                <ul>
                    <li><code>dateRange</code>
                    <ul>
                        <li><code>fromDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                        <li><code>toDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                    <li><code>date</code>
                    <ul>
                        <li><code>@standardDate</code></li>
                        <li><code>@localType</code></li>
                        <li><code>@notBefore</code></li>
                        <li><code>@notAfter</code></li>
                    </ul></li>
                    <li><code>descriptiveNote</code></li>
                    <li><code>placeRole</code></li>
                    <li><code>placeEntry</code>
                    <ul>
                        <li><code>@latitude</code></li>
                        <li><code>@longitude</code></li>
                        <li><code>@localType</code></li>
                        <li><code>@administrationCode</code></li>
                        <li><code>@countryCode</code></li>
                        <li><code>@vocabularySource</code></li>
                        <li><code>@certaintyScore</code></li>
                        <li><code>placeEntry</code></li>
                        <li><code>placeEntryBestMaybeSame</code>
                        <ul>
                            <li><code>@latitude</code></li>
                            <li><code>@longitude</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@administrationCode</code></li>
                            <li><code>@countryCode</code></li>
                            <li><code>@vocabularySource</code></li>
                            <li><code>@certaintyScore</code></li>
                        </ul></li>
                        <li><code>placeEntryLikelySame</code>
                        <ul>
                            <li><code>@latitude</code></li>
                            <li><code>@longitude</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@administrationCode</code></li>
                            <li><code>@countryCode</code></li>
                            <li><code>@vocabularySource</code></li>
                            <li><code>@certaintyScore</code></li>
                        </ul></li>
                        <li><code>placeEntryMaybeSame</code>
                        <ul>
                            <li><code>@latitude</code></li>
                            <li><code>@longitude</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@administrationCode</code></li>
                            <li><code>@countryCode</code></li>
                            <li><code>@vocabularySource</code></li>
                            <li><code>@certaintyScore</code></li>
                        </ul></li>
                    </ul></li>
                </ul></li>
                <li><code>localDescription</code>
                <ul>
                    <li><code>@localType (AssociatedSubject, nationalityOfEntity, gender)</code></li>
                    <li><code>term</code></li>
                </ul></li>
                <li><code>languageUsed</code>
                <ul>
                    <li><code>language</code>
                    <ul>
                        <li><code>@languageCode</code></li>
                        <li><code>@scriptCode</code></li>
                    </ul></li>
                </ul></li>
                <li><code>occupation</code>
                <ul>
                    <li><code>term</code>
                    <ul>
                        <li><code>@vocabularySource</code></li>
                    </ul></li>
                    <li><code>descriptiveNote</code></li>
                    <li><code>dateRange</code>
                    <ul>
                        <li><code>fromDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                        <li><code>toDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                </ul></li>
                <li><code>function</code>
                <ul>
                    <li><code>@localType</code></li>
                    <li><code>term</code>
                    <ul>
                        <li><code>@vocabularySource</code></li>
                    </ul></li>
                    <li><code>descriptiveNote</code></li>
                    <li><code>dateRange</code>
                    <ul>
                        <li><code>fromDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                        <li><code>toDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                </ul></li>
                <li><code>biogHist</code></li>
                <li><code>generalContext</code></li>
                <li><code>legalStatus</code>
                <ul>
                    <li><code>term</code>
                    <ul>
                        <li><code>@vocabularySource</code></li>
                    </ul></li>
                </ul></li>
                <li><code>mandate</code></li>
                <li><code>structureOrGenealogy</code></li>
            </ul></li>
            <li><code>relations</code>
            <ul>
                <li><code>cpfRelation</code>
                <ul>
                    <li><code>@arcrole</code></li>
                    <li><code>@href</code></li>
                    <li><code>@role</code></li>
                    <li><code>@type</code></li>
                    <li><code>@cpfRelationType</code></li>
                    <li><code>relationEntry</code></li>
                    <li><code>descriptiveNote</code></li>
                    <li><code>dateRange</code>
                    <ul>
                        <li><code>fromDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                        <li><code>toDate</code>
                        <ul>
                            <li><code>@standardDate</code></li>
                            <li><code>@localType</code></li>
                            <li><code>@notBefore</code></li>
                            <li><code>@notAfter</code></li>
                        </ul></li>
                    </ul></li>
                    <li><code>date</code>
                    <ul>
                        <li><code>@standardDate</code></li>
                        <li><code>@localType</code></li>
                        <li><code>@notBefore</code></li>
                        <li><code>@notAfter</code></li>
                    </ul></li>
                </ul></li>
                <li><code>resourceRelation</code>
                <ul>
                    <li><code>@role</code></li>
                    <li><code>@href</code></li>
                    <li><code>@type</code></li>
                    <li><code>@arcrole</code></li>
                    <li><code>relationEntry</code>
                    <ul>
                        <li><code>@localType</code></li>
                    </ul></li>
                    <li><code>objectXMLWrap</code></li>
                    <li><code>descriptiveNote</code></li>
                </ul></li>
            </ul></li>
        </ul></li>
    </ul></li>
</ul>

## Caveats on Parsing

There are a few caveats on parsing to convert the EAC-CPF XML file into an Identity Constellation.  They are:

* The `control` block's `languageDeclaration` tag data is parsed into the biogHist data structure, since that should be one of the only language-dependent portions of the description.
