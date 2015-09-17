We have had many problems due to inconsistent import data practices and formats. In addition to guidelines, we can create portable and online software to help people detect issues in their data. We already have many of these scripts in a state of maturity suitable for software developers. It is possible to raise that level of maturity to something we can offer to the general public.

Some of the issues encountered:

- data not encoded as utf8 files, or non-utf8 characters in the files. If there is a requirement to process non-utf8 files, those files will need to have the encoding explicitly set somewhere. It is impossible to always guess the encoding of a file. 

- illegal character encodings

- accidental inclusion of substituted characters. Commonly smart quotes, en-dash, em-dash. Fixing these requires running the data through cleaning software that we may always need due to people writing text in Microsoft product, then copying/pasting. The cleanup is easy, but ideally the bad characters wouldn't be in the data in the first place. 

- non well-formed XML. 

- lack of persistent id values. Every file should have a unique id. Every online finding aid needs a unique, persistent URI that is also a persistent URL and which loads in a web browser, and can be retrieved via wget or curl.

- URLs should resolve to static HTML without using JavaScript to dynamically build pages.

- ideally, any data-centric URL should have variant formats for humans (HTML, PDF) and computers (XML, JSON, etc.) 

- inclusion of URIs within data files. It creates issues to have to build a URI algorithmically based on an ID value found somewhere in a data file. The URI should have a standard (conventional) location in the data, and the URI should occur as a URL. Ideally, the ID value is also present simply as an ID. It should not be required to perform sub-string or regular expression operations to extract ID values. 

- URLs that are missing should return 404, and no other value. It is problematic for automated validation of URLs when a missing page returns a 200 (or any non-404 value) along with a human readable HTML message about the status of the page. 

