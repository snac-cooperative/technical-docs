# SNAC Ark Resolution

Correct ark resolution is tricky, especially when persistent identifiers must remain persistent while allowing merging and splitting of the identities to which they refer.  Consider the following two simple examples, a merge and a split, with simplistic arks A-F.  


![Merge-Split Simple Diagram](http://gitlab.iath.virginia.edu/snac/Documentation/raw/master/Specifications/Originals/merge-split-diagram.png)

In the first case, the identities for arks A and B are merged together and assigned a new ark, C.  From that point on, when a user requests the identity for arks A, B, or C, they should be given the identity to which ark C is assigned.  The second case is more complicated; the identity for ark D is split apart into two new identities, each being assigned a new ark (E and F).  After this split, when a user requests the identity for ark D, they should be given the "current" form of the identity, i.e. the identities to which E and F are assigned.  The user should be presented with both or a mechanism for choosing which new identity to which they were actually referring.  In either case here, when a user requests A, B, or D, they should be notified that those arks, while still permanent, now refer to an identity better referred to by another ark.

These simplistic examples are only the building blocks of the more complex ark resolution structure we must consider.  In fact, the full merge-split history would result in a directed acyclic graph (DAG), as partly evidenced below.

![Merge-Split Complicated Diagram](http://gitlab.iath.virginia.edu/snac/Documentation/raw/master/Specifications/Originals/merge-split-diagram2.png)

Here we see three original identities assigned arks A, B, and C.  They undergo many splits and merges to result in two updated identities, those assigned arks H and K, while many other persistent identifiers are created for the intermediary identities.  When the user requests arks C, E, G, or J, they should only be given the identity for ark K; however, requesting A, B, D, or F should result in the identities (or choice thereof) for arks H and K.

To handle this resolution process, we have chosen to use a lookup table instead of tracing the entire DAG.  Specifically we maintain a lookup table with shortcuts directly to the "bottom" of the DAG.  For the example above, we would store this simpler DAG, shown below.

![Merge-Split Augmented Diagram](http://gitlab.iath.virginia.edu/snac/Documentation/raw/master/Specifications/Originals/merge-split-diagram3.png)

We extrapolate this diagram into a 4-column table, mapping the original numeric identifier and ark to the current numeric identifier and ark.  This table is updated at each publish, merge, and split to keep the lookup up-to-date.

| IC Numeric Identifier | IC Ark Identifier | Current Numeric Identifier | Current Ark Identifier |
|:---------------------:|:-----------------:|:--------------------------:|:----------------------:|
| 1                     | A                 | 3                          | C                      |
| 2                     | B                 | 3                          | C                      |
| 3                     | C                 | 3                          | C                      |

## Challenges

We note that there are still some important challenges to overcome within the larger system.  Specifically,

- Intra-SNAC ID lookups must be able to handle the case when multiple current ICs are found for the id stored within Constellation relations, Resource repository links, and SNAC affiliation links
