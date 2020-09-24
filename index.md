---
layout: entry
title: Documentation for S800 corpus revision
---

## Revision steps

### 1. Decouple recognition from normalization, address overall consistency

The original S800 corpus data only annotated mentions that could be normalized to a particular (2013?) version of the NCBI Taxonomy. From the perspective of recognizing mentions of species names (regardless of normalization), this caused the annotation to appear incomplete in places. In the first revision step, annotation was added for scientific and common names of species regardless of whether they could be normalized, a revision pass addressing the overall consistency of annotation was performed, and annotated names of genera, families and other levels of taxonomy above species were marked Out-of-scope.

**Relevant commits**: [59fe5be](https://github.com/spyysalo/s800-revision-data/commit/59fe5be9fcf37ee0fd803336478559b5ea3f4692), [0165f52](https://github.com/spyysalo/s800-revision-data/commit/0165f52a50d3960ac2f43fbfd9b4d625f3c46c24)

### 2. Revise annotated spans for boundary consistency

The original evaluation using the S800 corpus data applied relaxed boundary matching criteria:

> [...] we used flexible boundary matching of species names, meaning that taggers would receive a true positive if it produced a tag that overlapped with an annotated substring and had the correct assigned taxonomic identifier.

Because of this matching criterion, the exact boundaries of annotated species mentions had little impact on the evaluation, and the annotation effort did not establish precise guidelines detailing which tokens should be included in annotated spans. Consequently, from the perspective of exact matching -- the criterion used e.g. by the popular `conlleval` evaluation script and most recent machine learning experiments on the corpus -- the boundaries of annotated mentions were inconsistently annotated in many places. To address this issue, we created detailed guidelines on how to determine annotation boundaries and made a revision pass addressing related issues in the data. This revision also included a focused review of the annotation of virus mentions, which had comparatively frequent annotation boundary issues.

**Relevant commits**: [112f0b2](https://github.com/spyysalo/s800-revision-data/commit/112f0b2e256707aa94039bf8e4b3ae3b7bdaa7b6), [7012e42](https://github.com/spyysalo/s800-revision-data/commit/7012e42ac2c22579d51ba6766185961fc548b5a9)

### 3. Separate strain mentions from species mentions

The original S800 corpus annotation only involves a single annotated mention type (species) that is used to annotate mentions of species names as well as mentions of more specific names such as those of strains. In the revision, we introduced a separate `Strain` type and revised all strain name mention to use this type, also revising the spans of species annotation to exclude strain names when the two occurred together in text.

**Relevant commits**: [5212baf](https://github.com/spyysalo/s800-revision-data/commit/5212baf17dd63670ef1f987c1fe9949582b3f98c), [489fdf1](https://github.com/spyysalo/s800-revision-data/commit/489fdf16cb85c8b90aeff7acd19aa9cc5905440e)

### 4. Introduce annotation for upper taxonomic ranks

The original S800 corpus annotation included partial annotation for mentions of names at taxonomic ranks above species, in particular in a number of cases where these names were used in an imprecise way to refer to species (e.g. _Drosophila_ for _Drosophila melanogaster_). As the initial revision step addressing annotation consistency had marked these as `Out-of-scope`, the coverage of the revised annotation was in some aspects reduced from that of the original corpus. In this revision step, annotation for mentions of names at taxonomic ranks above species was reintroduced in a systematic way, adding e.g. `Genus` as a distinct annotated type.

**Relevant commits**: [20ccfb7](https://github.com/spyysalo/s800-revision-data/commit/20ccfb7eddefb8d675fb9405786efb3e88c4d6b9)

## Original Curator Guidelines for S800 corpus

The annotation guidelines as described in the [original publication of the S800 corpus](https://journals.plos.org/plosone/article/file?id=10.1371/journal.pone.0065390&type=printable)

The guidelines to curators were to annotate all substrings, which can meaningfully be identified as _referring to a taxon_. While the main focus was on annotating __species__ mentions, strings referring to __any taxonomic level__, (e.g.kingdoms, orders, genera, strains) were also considered. These data were very likely never released.

The main guidelines were:

* All document substrings must be evaluated and all mentions including repetitions should be listed in the order of appearance in the text.
* The annotated name types among others should include: Linnaean binomials, common names, strain names, author defined acronyms.
* For each annotated string, curators must record the name as it appeared in text and report the corresponding NCBITaxonomy database identifier.
* Special cases of adjectives being used to indicate a taxon, misspellings, typographic or other errors and enumerations were indicated as such.
* Taxonomic mentions that did not correspond to an existing NCBI Taxonomy database entry were also indicated.

## Inconsistencies detected that initially seem to contrast the above guidelines

* Some fairly obvious species names lack annotation for unidentified reasons. For examples, see mentions of _Escherichia coli_ in [20971900](https://pubmed.ncbi.nlm.nih.gov/20971900/) and [21324422](https://pubmed.ncbi.nlm.nih.gov/21324422/), _Pseudomonas aeruginosa_ in [21075931](https://pubmed.ncbi.nlm.nih.gov/21075931/), and  _Flavobacterium sinopsychrotolerans_ in [20118284](https://pubmed.ncbi.nlm.nih.gov/20118284/).
* Inconsistencies in tagging of strains (e.g. strains are not tagged in [20118285](https://pubmed.ncbi.nlm.nih.gov/20118285/)
* genera etc. tended not to have any annotation, e.g. 4/7 first documents (numeric order):
  * the genus __Serinicoccus__ (x3) in [20118285](https://pubmed.ncbi.nlm.nih.gov/20118285/)
  * the genus __Streptococcus__ (x2) in [20139281](https://pubmed.ncbi.nlm.nih.gov/20139281/)
  * the __Bulleribasidium__ clade (x3) in [20139282](https://pubmed.ncbi.nlm.nih.gov/20139282/)
  * the genus __Scardovia__ in [20139283](https://pubmed.ncbi.nlm.nih.gov/20139283/)
* There appears to be some inconsistency around the tagging of upper-level taxonomical terms such as __virus__ and __viral__, which are tagged in [20980513](https://pubmed.ncbi.nlm.nih.gov/20980513/) but not in over 100 other cases.
* The adjectival form __pneumococcal__ is annotated in 7/12 cases in S800 although the general rule appears to be that adjectival forms are not annotated (e.g. __murine__, __bovine__, __cyanobacterial__) in contrast to guidelines.
* There is also an apparently inconsistent treatment of common species names, with e.g. __mouse__, __swine__, __trout__ and __rat__ partially annotated, but e.g. __goat__, __horse__, __crab__, and __rats__ never receiving annotation in S800. (__rat__ vs. __rats__ being a particularly clear case)

A complete list of inconsistencies can be viewed in this [table](https://docs.google.com/spreadsheets/d/1aS3djov-RaPiJ_U4z5Smx8YY2spAPTFUvmoEqtUIj6Y/edit?usp=sharing) if it is filtered down to terms annonated in the S800 corpus. See also [Comparison of annotations between S800 and LINNEAUS](#comparison-of-annotations-between-s800-and-linneaus).

## Other inconsistencies in the annotation

These are mainly annotation boundary issues. Correct annotation boundaries was not a requirement in the original corpus annotation guidelines and was not taken into account when reporting F-scores on the datasets (_"For the mention-level statistics we used flexible boundary matching of species names, meaning that taggers would receive a true positive if it produced a tag that overlapped with an annotated substring and had the correct assigned taxonomic identifier. For example, if the string “E. coli K12” is annotated in S800 and the tagger matches only the string “E. coli”, it will be counted as a true positive (provided the taxonomic identifier is also correct)."_).

* The expressions _sp. nov._ and _gen. nov., sp. nov._ following a species name are included in annotated spans in some but not all cases. For example, these are part of the annotated spans at the start of documents [20118285](https://pubmed.ncbi.nlm.nih.gov/20118285/), [20139281](https://pubmed.ncbi.nlm.nih.gov/20139281/), and [20154326](https://pubmed.ncbi.nlm.nih.gov/20154326/), but not [19667393](https://pubmed.ncbi.nlm.nih.gov/19667393/), [20139283](https://pubmed.ncbi.nlm.nih.gov/20139283/) and [20139284](https://pubmed.ncbi.nlm.nih.gov/20139284/)
* The string __(T)__ appearing at the end of strain names (superscript T in the original text) is included in annotated spans in some but not all cases. For examples, compare e.g. [19667393](https://pubmed.ncbi.nlm.nih.gov/19667393/) and [20118285](https://pubmed.ncbi.nlm.nih.gov/20118285/).
* When the name of the person who first named a species is mentioned in parenthesis after the species name (e.g. _Diabrotica virgifera virgifera_ (LeConte)), this is annotated in some but not all cases. Also, the same question arises for the non-parenthesized forms such as _Pseudacteon tricuspis_ Borgmeier.
* tomato vs. tomato plant(s)
* HCV vs. anti-HCV
* niger vs. A. niger strain
* galaxias vs. native galaxias
* Salmonella enterica vs. Salmonella enterica serovar Typhimurium
* (this example is extra) Influenza vs. Influenza vaccine

## Additional guidelines for reannotation of the S800 corpus

### Span consistency guidelines

* Do __not__ include the expressions _sp. nov._ and _gen. nov., sp. nov._ in the species name, since these are supposedly used only the first time a genus and/or a species/subspecies is described to denote that it's new, so they are not part of the scientific name and shouldn't be found anywhere else other than the first paper describing them.
* An annotated span should __not__ end with _sp._ or _spp._
* Superscript T to denote type strain should __not__ be included in species' names
* The person's name should __not__ be included in the species name, especially when it is in parentheses. The non-parenthesized form is a bit more complex (at least in the example above _Pseudacteon tricuspis_ Borgmeier is a valid name shown as a synonym for _Pseudacteon tricuspis_ in NCBI taxonomy). For annotation consistency the suggestion is to __drop these names in all appearances__. (The confusion with subspecies can be avoided because of the capital letter at the start of the second word, e.g. _Ursus arctos arctos_ would be easy to distinguish from _Ursus arctos_ Linneaus and then drop the name for the latter.)
* Do __not__ include common head nouns such as "plants" in annotation span

* Do __not__ include adjectival premodifiers such as "native" in annotation span
* Model words like __SCID__ mouse should be excluded from annotations
* _"species complex"_ should __not__ be part of a species name, e.g. from 20682355

~~~ann
The splicing activity of the PRP8 intein from the B. dermatitidis, E. parva and P. brasiliensis species complex was demonstrated in a non-native protein context in Escherichia coli.
T5	Species 50 65	B. dermatitidis
T6	Species 67 75	E. parva
T7	Species 80 95	P. brasiliensis
T8	Species 164 180	Escherichia coli
~~~

* _f. sp._ (forma specialis) should be included in the annotated mention (e.g. _Blumeria graminis f. sp. tritici_) 
* Do __not__ include nouns identifying levels of taxonomy in annotation spans. For example, the words __strain__, __serotype__, __serovar__, and __serogroup__ should be excluded from the spans of annotated _Strain_ mentions. e.g from 20154326
~~~ ann
Strain GSW-R14(T) exhibited 97.6 % 16S rRNA gene sequence similarity ...
T1 Strain 7 14 GSW-R14
~~~
* Annotate antibodies e.g __anti-HCV__ with species annotation for the organism (__HCV__) and _Note: "anti-" prefix_

### general annotation guidelines

* Preprocessing errors (e.g. & amp;) should be fixed
* Adjectival forms like __murine__ (__taxid:10090__), __bovine__ (__taxid:9913__), __pneumococcal__ (__taxid:1313__) that map to a specific species should be annotated as such
* Adjectival forms of Phyla (e.g. __cyanobacterial__: __taxid:1117__) can only be annotated as __OOS__ or not be annotated at all
* Adjectival forms of __Kingdoms__ (e.g. __viral__, __bacterial__) can only be annotated as __OOS__ or not be annotated at all
* Introduce a flag-attribute for __cannot be normalized__ for cases that are not full names (but only understandable as references in context) e.g. _strips of types O, A and Asia 1_
* Non-name mentions (e.g. __woman__) and species clues (e.g. __patients, children, men, women__) should not be annotated. This includes the non-name mention __man__ which should not be annotated as a synonym for _Homo sapiens_ (__taxid: 9606__)
* The role in which common species names are mentioned should __not__ be taken into account and all species names mentions should be annotated (e.g. _rice_ mentioned as food or _tobacco_ as cigarettes should still be annotated).
* Genus or higher level mentions (e.g. _Arabidopsis_, _yeast_) should only be annotated as the real taxinomic level (i.e. _genus_, _phylum_) and not as synonyms of species names. (e.g. _Arabidopsis_ should be annotated as __OOS__ and assigned the genus __taxid:3701__) 

~~~ ann
The second face of a known player: Arabidopsis silencing suppressor AtXRN4 acts organ-specifically
T1 Out-of-scope 35 46	Arabidopsis
N1	Reference T1 Taxonomy:3701	Arabidopsis
~~~

* Former _Species_ annotations that belong to the following taxinomic ranks: _Class_, _Order_, _Family_ and _Genus_ have been annotated as the latter in the corpus. Ranks higher than _Class_ (e.g. _Phylum_, _Kingdom_) should receive an _Out-of-scope_ annotation (NCBI Taxonomy Ranking adopted from [Schoch, _et. al_, 2020](https://academic.oup.com/database/article/doi/10.1093/database/baaa062/5881509#206172258)
* For annotations above _Species_ only the "coarse" ranks should be considered, thus mapping mentions at fine-grained levels to their coarse equivalents, e.g. _Subgenus_ -> _Genus_, _Subfamily_ -> _Family_ etc. Some examples are given below:
  * _Subfamily_: _Plusiinae_ --> __Family__
  * _Superfamily_: _butterflies_ --> __Family__
  * _Infraorder_: _snakes_ --> __Order__
  * _Infraorder_: _planthoppers_ --> __Order__
  * _Suborder_: _true bugs_ --> __Order__
  * _Suborder_: _Heteroptera_ --> __Order__
  * _Infraorder_: _dragonflies_ --> __Order__
* __common name__ (_scientific name_)" mentions should be annotated as __two mentions__ e.g from 21054435:

~~~ ann
We studied seasonal dynamics in delta^1^3C of CO2 efflux (delta^1^3C(E)) from non-leafy branches, upper and lower trunks and coarse roots of adult trees, comparing deciduous Fagus sylvatica (European beech) with evergreen Picea abies (Norway spruce).
T1 Species 174 189 Fagus sylvatica
T2 Species 191 205 European beech
T3 Species 222 233 Picea abies
T4 Species 235 248 Norway spruce
~~~

#### Strains

* Strain aliases such as __CC-12301__(T) (=__DSM 45298__(T) =__CCM 7727__(T)) should be annotated in all instances as type __Strain__.
* _name strain_ mentions should be annotated as __two mentions__ of __Species__+__Strain__, e.g. from 20154326

~~~ ann
Strain GSW-R14(T) exhibited 97.6 % 16S rRNA gene sequence similarity to F. gelidilacus LMG 21477(T) and similarities of 91.2-95.2 % to other members of the genus Flavobacterium
T1 Species 7 14 GSW-R14
T2 Species 72 86 F. gelidilacus
T3 Strain 87 96 LMG 21477
~~~

#### Viruses

* __Viruses__ (or other taxonomic units) that have species level of entry as "unidentified" (e.g. "retrovirus" __taxid:31931__ ("unidentified retrovirus" equivalent: "retrovirus") or "adenovirus" __taxid:10535__ ("unidentified adenovirus" equivalent: "adenovirus")) should __NOT__ be annotated in the species level, but should be annotated at the __higher taxonomic rank__ that better describes them (e.g. family rank for Retroviridae, Adenoviridae etc)
  * "virus"/"viral" __OOS__+__taxid:10239__ "Viruses" _superkingdom_
  * "retrovirus" __OOS__+__taxid:11632__ "Retroviridae" _family_
  * "influenza virus" __OOS__+__taxid:11308__ "Orthomyxoviridae" _family_
  * "herpesvirus" __OOS__+__taxid:10292__ "Herpesviridae" _family_
  * "adenovirus" __OOS__+__taxid:10508__ "Adenoviridae" _family_
  * "baculovirus" __OOS__+__taxid:10442__ "Baculoviridae" _family_
  * "reovirus" __OOS__+__taxid:10880__ "Reoviridae" _family_
  * "norovirus" __OOS__+__taxid:142786__ "Norovirus" _genus_
  * "ebola virus" __OOS__+__taxid:186536__ "Ebolavirus" _genus_
  * "cytomegalovirus" __OOS__+__taxid:10358__ "Cytomegalovirus" _genus_
* __dengue__: dengue is synonym for dengue fever (disease), annotate as  __OOS__ + __no taxid__ unless _dengue virus_ is mentioned when it should be annotated as __taxid:12637__ (species)
* __smallpox__: smallpox is synonym for smallpox disease, annotate as  __OOS__ + __no taxid__ unless _smallpox virus_ is mentioned when it should be annotated as __taxid:10255__ (species)
* __influenza__: influenza is synonym for the flu (disease), annotate as  __OOS__ + __no taxid__ unless _influenza X virus_ is mentioned when it should be annotated as _Species_

#### Yeasts

* Discontinuous entities should be annotated as such (e.g. http://ann.turkunlp.org:8088/index.xhtml#/S800/20933017?focus=610~643)
* all text spans including "yeast" should have an __Out-of-scope__ annotation if the taxonomy level is higher than Species:
  * standalone _yeast_: __OOS__+__taxid:147537__ ("true yeast" subphylum) (Note: an even higher level may be included)
  * _black yeast_: __OOS__+__taxid:34395__ ("black yeast" order)
  * _budding yeast_: __OOS__+__taxid:4892__ ("budding yeasts" order)
  * _fission yeast_: __OOS__+__taxid:4894__ ("fission yeasts" family)
  * _truffle_: __OOS__ + __taxid:36048__ (_Tuber_ genus)

#### Common names
* In general, when a species and a higher-level entry in the taxonomy (e.g. genus) share a common name or synonym, the __species interpretation should be preferred__ when it is not clear from context which is intended.
* Common names like __human__, __goat__, __horse__, and __rats__ should be __always__ annotated.
* Common names that should not be annotated in the species level:
  * __fire ant__: several species of ants, tag as __OOS__ and __no taxid__ ( red fire ant, little fire ant, black fire ant etc should be tagged as the corresponding species)
  * __sunflower__: __OOS__+__taxid:4231__ (__Helianthus__ genus)
  * __galaxias__ : __OOS__+__taxid:51242__ (__Galaxias__ genus)
  * __trout__: several species of fish, annotate as __OOS__ + __no taxid__
  * __leafminer__: insects that eat the tissue of plants, annotate as __OOS__ + __no taxid__
  * __elephant__: 3 species, not monophyletic (both __Elephas__ and __Loxodonta__ genera), annotate as __OOS__ + __no taxid__
  * __crab__: infraorder containing 850 species, so it should be annotated as __OOS__ + __taxid:6752__ (__Brachyura__ infraorder)
* Common names that should be annotated in the species level (but could be annotated in a higher taxonomic level)
  * __rat__: synonym for _Rattus norvegicus_ and __Rattus__. Should be annotated as _Rattus norvegicus_ (__taxid:10116__), unless explicitly referring to a different taxonomic unit (e.g. __cotton rat__: __OOS__ + __taxid:42414__ (__Sigmodon__ genus))
  * __fruit fly__: synonym for _Drosophila melanogaster_ and __Drosophila__ genus and __Tephritidae__ family. Should be annotated as _Drosophila melanogaster_ (__taxid:7227__), unless explicitly referring to a different taxonomic unit
  * __bee__: synonym for _Apis mellifera_, and __Apoidea__ superfamily. Should be annotated as _Apis mellifera_ (__taxid:7460__), unless explicitly referring to a different taxonomic unit (e.g. __bumble bee__)
  * __duck__: synonym for _Anas platyrhynchos_, but can be a synonym for other __Anatidae__. Should be annotated as _Anas platyrhynchos_ (__taxid:8839__), unless explicitly referring to a different taxonomic unit
  * __midge__: synonym for _Chironomus thummi_, but can refer to several species of flies. Should be annotated as _Chironomus thummi_ (__taxid:7154__), unless explicitly referring to a different taxonomic unit


### Guidelines pending to be applied/decided upon

* Annotate young animals (e.g. chicks, calfs etc). -- Contradicting with rule about annotating child/children

## Experiments to automatically correct inconsistencies on the corpus

Based on a quick check, it appears that _(T)_ is included in the span of 65 annotations in the corpus (~1.7%) and excluded from the span of 79 (~2.1%). _sp. nov_ is included in the span of 30 annotations and excluded from the span of 21. Sampo wrote a script to add _(T)_ and remove _sp. nov._ from annotations when it was present which led to 2% point improvement in F-score for the deep learning model
`Original: train_batch_size 4 learning_rate 2e-5 num_train_epochs 3: 78.77`
`Updated: train_batch_size 24 learning_rate 2e-5 num_train_epochs 4: 80.76`

Quick experiment comparing S800 performance with exact span matching to relaxed criteria. This is using the original version of the corpus to maintain comparability:

* __exact matching__: `precision 69.95% (603/862) recall 78.62% (603/767) F 74.03%`
* __left boundary__: `precision 77.03% (664/862) recall 86.57% (664/767) F 81.52%`
* __right boundary__: `precision 72.51% (625/862) recall 81.49% (625/767) F 76.73%`
* __overlap__: `precision 80.16% (691/862) recall 88.92% (682/767) F 84.31%`

Here, "left/right boundary" only requires the left/right boundaries of predicted and gold spans to match, and "overlap" accepts any overlap between a predicted and gold spans as a match. A few notes:

* "overlap" most closely resembles the criterion applied by Pafilis _et al._, and it's good to note that we're outperforming them for this criterion.
* The 10% point absolute difference (nearly 40% of the F-score error) between the exact and overlap results suggests that errors very frequently relate to mismatched entity boundaries (rather than fully missing an entity mention or predicting an entirely extra one)
* "left boundary" gives nearly as high performance as "overlap", whereas "right boundary" is closer to the "exact" result, indicating that most of the boundary issues are at the end of the spans. This matches our manual examination identifying _(T)_ and _sp. nov._ and the like
* The difference between these relaxed matching results and the exact matching results for our quick revision of the corpus suggests that there may be systematic differences in spans that we didn't notice yet.

## Comparison of annotations between S800 and LINNEAUS

[Comparison Table](https://docs.google.com/spreadsheets/d/1aS3djov-RaPiJ_U4z5Smx8YY2spAPTFUvmoEqtUIj6Y/edit?usp=sharing)

1. Annotated in LINNAEUS, one or more appearances in S800, never annotated in S800:
_patients, patient, people, women, fly, children, flies, salmon, moth, child, rodent, murine, cyanobacterial, man, infants, participants, Patients, shrimp, crab, rats, bovine, Potato, pea, person, infant, Penaeus monodon, men, persons, calf, goat, horse, Children, Bacillus cereus group, smallpox, sorghum, guinea pigs, fission yeast, Sorghum bicolor, Blumeria graminis_

2. Annotated one or more times in S800, but not in all cases (manual selection from a large list):
_human, Escherichia coli, HIV, yeast, rice, mice, HCV, maize, mouse, tomato, wheat, HDV, VZV, HBV, tobacco, Arabidopsis, C. neoformans, E. coli, NDV, Cryptococcus neoformans, Pseudomonas aeruginosa, FGSC, Drosophila, galaxias, pneumococcal, corn, influenza virus, U. maydis, Zea mays, Fusarium graminearum, Bdellovibrio, trout, P. aeruginosa, elephant, Solenopsis invicta, Salmonella enterica, swine, influenza A H1N1, fire ant_

3. Species clues (e.g. __patients, children, men, women__) are annotated in LINNEAUS, but not annotated in S800.

### LINNEAUS corpus tag filtering

In addition to the primary annotation file __tags.tsv__, the distribution includes the file __filtered_tags.tsv__, described in the documentation as follows:

* __filtered_tags.tsv__ -- a filtered version of tags.csv where only NCBI-taxonomy-names are included (i.e. "species clues" such as "patient", "woman", etc. have been removed)

Comparing this with tags.tsv gives us an idea of what the LINNAEUS authors/annotators tagged but did not consider an NCBI taxonomy name:

<pre><code>diff tags.tsv filtered_tags.tsv | egrep '^<' | cut -f 5 | sort | uniq -c | sort -rn
437 patients
159 patient
112 people
111 women
80 men
71 participants
66 murine
62 children
51 persons
31 calf
27 calves
22 Patients
19 person
18 guinea-pigs
18 Participants
18 MRSA
17 participant
12 guinea-pig
12 chick
10 boy
5 child
5 Women
5 Persons
5 Children
4 woman
4 People
3 Patient
3 Guinea-pigs
2 infants
2 girls
2 boys
2 Murine
2 Men
2 Child
2 Calves
1 text
1 schoolchildren
1 peoples
1 infant
1 girl
1 Person
1 Participant
1 Calf</code></pre>

## External links to LINNEAUS and S800 corpora

* [version 1.1 release of the LINNAEUS corpus](https://github.com/spyysalo/linnaeus-corpus-data)
* [version 1.0 of s800 corpus](http://species.jensenlab.org/files/S800-1.0.tar.gz)

## Repositories for conversion to CONLL format

* [LINNAEUS](https://github.com/spyysalo/linnaeus-corpus)
* [S800](https://github.com/spyysalo/s800)

## Other repositories relevant to this project
* [Hartuu's S800 examiner](https://harttu.github.io/s800_ambiguous_html/)
* [S800 original in BRAT](http://ann.turkunlp.org:8088/index.xhtml#/S800-original/)
* [S800 revision in BRAT](http://ann.turkunlp.org:8088/index.xhtml#/S800/)

For information on Annodoc, see <http://spyysalo.github.io/annodoc/>.
