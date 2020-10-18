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

* The first resource that is trusted to resolve issues is [NCBI Taxonomy](https://www.ncbi.nlm.nih.gov/taxonomy). If there is still not enough information there then the [Catalogue of Life](http://www.catalogueoflife.org/) is advised

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
* Genus or higher level mentions (e.g. _Arabidopsis_, _yeast_) should only be annotated as the real taxinomic level (i.e. _genus_, _phylum_) and not as synonyms of species names. (e.g. _Arabidopsis_ should be annotated as `Genus` and assigned the genus __taxid:3701__) 
~~~ ann
The second face of a known player: Arabidopsis silencing suppressor AtXRN4 acts organ-specifically
T1 Genus 35 46	Arabidopsis
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
* _Tribe_ and _Subtribe_ are also normalized to __Family__
* For __Subspecies__ mentions: when a subspecies name immediately follows a species name the entire mention is simply annotated as one slightly longer Species mention, e.g. _Phocoenoides dalli dalli_ annotated as __Species__ + __taxid: 9745__ (Rank: Subspecies).
* __common name__ (_scientific name_)" mentions should be annotated as __two mentions__ e.g from 21054435:
~~~ ann
We studied seasonal dynamics in delta^1^3C of CO2 efflux (delta^1^3C(E)) from non-leafy branches, upper and lower trunks and coarse roots of adult trees, comparing deciduous Fagus sylvatica (European beech) with evergreen Picea abies (Norway spruce).
T1 Species 174 189 Fagus sylvatica
T2 Species 191 205 European beech
T3 Species 222 233 Picea abies
T4 Species 235 248 Norway spruce
~~~
* Forms identified by place names, like __ecotype__, are not annotated.
~~~ ann
For investigating cadmium uptake, we incubated protoplasts obtained from leaves of Thlaspi caerulescens (Ganges ecotype) with a Cd-specific fluorescent dye.
T1 Species 83 103 Thlaspi caerulescens
~~~
* Cultivars should be annotated as __OOS__
~~~ ann
The physiological traits underlying the apparent drought resistance of 'Tomatiga de Ramellet' (TR) cultivars.
T1 Out-of-scope 72 92 Tomatiga de Ramellet
~~~
* Non-taxonomic groupings such as __Gram-positive/negative bacteria__, __marine bacteria__ or __enteric bacteria__ should not be annotated. e.g.
~~~ann
The redox-sensitive transcription factor SoxR in enteric bacteria senses and regulates the cellular response to superoxide and nitric oxide.
~~~
* The last rule includes cases like the following from 21037015
~~~ ann
Oscillochloris trichoides is a mesophilic, filamentous, photoautotrophic, nonsulfur, diazotrophic bacterium which is capable of carbon dioxide fixation via the reductive pentose phosphate cycle and possesses no assimilative sulfate reduction.
T1 Species 0 25 Oscillochloris trichoides
~~~
* __tree__ and __bush__ are non-taxonomic mentions and thus not annotated
* Species names also in noun phrase premodifier positions (e.g. __Arabidopsis__ EDR1, __Aspergillus nidulans__ cells) also in cases where they appear as part of the name of an entity of a non-organism type (e.g. __human__ epidermal growth factor receptor 2 (HER2)) are annotated.
* Abbreviations are marked if the abbreviation stands for an organism mention in scope of the annotation, but not if the full form merely includes an organism mention e.g. in modifier position. For example, the __H__ in __HER2__ is not annotated despite it standing for __human__.
* Standalone __alga__ (__algae__): __OOS__ + __no taxid__ [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/17259190?focus=T6) Algae is an informal term for a large and diverse group of photosynthetic eukaryotic organisms.
* __protist__ (any eukaryotic organism that is not an animal, plant, or fungus) is a non-taxonomical expression and will be annotated as __OOS__ + __no taxid__ [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/7708661?focus=T18)
* __marsupial__ (animals carry the young in a pouch) is a mammalian clade, [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/7700877) and will be annotated as __OOS__ + __taxid:9263__
* __methanotroph__ is a non-taxonomical expression and will be annotated as __OOS__ + __no taxid__ [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/1969923?focus=T1)
* __prokaryotes__ includes _Bacteria_ and _Archaea_ in the current three-domain system, so this will be annotated as __OOS__ + __no taxid__, despite the fact that __eukaryotes__ will be annotated as __OOS__ + __taxid:2759__ [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/1969923?focus=T15) and [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/1969923?focus=T16)
* __heterokonts__ and __alveolates__ are clades of microorganisms and will be annotated as __OOS__ + __taxid:33634__ and __taxid:33630__ respectively [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/9783459?focus=T18) and [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/9783459?focus=T19)
* __cyanobacteria__, __eubacteria__ and the like should be annotated as __OOS__ unless it's clear from context that the reference is definitely to the genus __Cyanobacterium__ or __Eubacterium__ respectively.
* __Young animals__ (e.g. chicks, calfs etc) should __NOT__ receive an annotation or should receive an __OOS__ annotation

#### Strains

* Strain aliases such as __CC-12301__(T) (=__DSM 45298__(T) =__CCM 7727__(T)) should be annotated in all instances as type __Strain__.
* _name strain_ mentions should be annotated as __two mentions__ of __Species__+__Strain__, e.g. from 20154326
~~~ ann
Strain GSW-R14(T) exhibited 97.6 % 16S rRNA gene sequence similarity to F. gelidilacus LMG 21477(T) and similarities of 91.2-95.2 % to other members of the genus Flavobacterium
T1 Species 7 14 GSW-R14
T2 Species 72 86 F. gelidilacus
T3 Strain 87 96 LMG 21477
~~~
* mentions of the form _[Genus] sp. [Strain]_, should have a separate __Genus__ and __Strain__ annotation [e.g.](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/9268298?focus=37~59)

#### Viruses

* __Viruses__ (or other taxonomic units) that have species level of entry as "unidentified" (e.g. "retrovirus" __taxid:31931__ ("unidentified retrovirus" equivalent: "retrovirus") or "adenovirus" __taxid:10535__ ("unidentified adenovirus" equivalent: "adenovirus")) should __NOT__ be annotated in the species level, but should be annotated at the __higher taxonomic rank__ that better describes them (e.g. family rank for Retroviridae, Adenoviridae etc)
  * "virus"/"viral" __OOS__+__taxid:10239__ "Viruses" _superkingdom_
  * "retrovirus" __Family__+__taxid:11632__ "Retroviridae" _family_
  * "influenza virus" __Family__+__taxid:11308__ "Orthomyxoviridae" _family_
  * "herpesvirus" __Family__+__taxid:10292__ "Herpesviridae" _family_
  * "adenovirus" __Family__+__taxid:10508__ "Adenoviridae" _family_
  * "baculovirus" __Family__+__taxid:10442__ "Baculoviridae" _family_
  * "reovirus" __Family__+__taxid:10880__ "Reoviridae" _family_
  * "norovirus" __Genus__+__taxid:142786__ "Norovirus" _genus_
  * "ebola virus" __Genus__+__taxid:186536__ "Ebolavirus" _genus_
  * "cytomegalovirus" __Genus__+__taxid:10358__ "Cytomegalovirus" _genus_
* __dengue__: dengue is synonym for dengue fever (disease), annotate as  __OOS__ + __no taxid__ unless _dengue virus_ is mentioned when it should be annotated as __taxid:12637__ (species)
* __smallpox__: smallpox is synonym for smallpox disease, annotate as  __OOS__ + __no taxid__ unless _smallpox virus_ is mentioned when it should be annotated as __taxid:10255__ (species)
* __influenza__: influenza is synonym for the flu (disease), annotate as  __OOS__ + __no taxid__ unless _influenza X virus_ is mentioned when it should be annotated as _Species_
* __human adenovirus__ (or similar cases): when a mention cannot be normalized in an "identified" virus species it should be annotated e.g. as __Species__+__taxid:9606__ (_Homo sapiens_) for __human__ and __Family__+__taxid:10508__ (_Adenoviridae_) for _adenovirus

#### Yeasts

* Discontinuous entities should be annotated as such (e.g. http://ann.turkunlp.org:8088/index.xhtml#/S800/20933017?focus=610~643)
* all text spans including "yeast" should have an __Out-of-scope__ annotation if the taxonomy level is higher than Species:
  * standalone _yeast_: __OOS__+__taxid:147537__ ("true yeast" subphylum) (Note: an even higher level may be included)
  * _black yeast_: __Order__+__taxid:34395__ ("black yeast" order)
  * _budding yeast_: __Order__+__taxid:4892__ ("budding yeasts" order)
  * _fission yeast_: __Family__+__taxid:4894__ ("fission yeasts" family)
  * _truffle_: __Genus__ + __taxid:36048__ (_Tuber_ genus)
  
#### Amoebae
* All __amoebae__ instances have been revised to resolve confusion of non-taxonomical expression __amoebae__ (type of cell or unicellular organism which has the ability to alter its shape), of __taxid:554915__ (__OOS__: _Clade: Amoebozoa_), and __taxid:55774__ (__Genus__: _Amoebae_). Most of the cases were non-taxonomical expressions (__OOS__ + __no taxid__)
  * __testate amoebae__: very common combination of mentions, which means _shelled amoebae_, which explains the form of microorganism(s): __OOS__ + __no taxid__
  * Interesting article [21112814](https://pubmed.ncbi.nlm.nih.gov/21112814/), where both _non-taxonomical_ and _Genus_ amoebae are mentioned (only one real "amoebae" Genus in the corpus)

#### Common names
* In general, when a species and a higher-level entry in the taxonomy (e.g. genus) share a common name or synonym, the __species interpretation should be preferred__ when it is not clear from context which is intended.
* Common names like __human__, __goat__, __horse__, and __rats__ should be __always__ annotated.
* Common names that should not be annotated in the species level:
  * __fire ant__: __Genus__ and __taxid:13685__ (__Solenopsis__); Note: red fire ant, little fire ant, black fire ant etc should be tagged as the corresponding species)
  * __ant(s)__: __Family__+__taxid:36668__ (__Formicidae__)
  * __insect(s)__: when standalone assign __Class__+__taxid:50557__ (__Insecta__)
  * __sunflower__: __Genus__+__taxid:4231__ (__Helianthus__)
  * __galaxias__ : __Genus__+__taxid:51242__ (__Galaxias__)
  * __mite__: __Class__+__taxid:6933__ (__Acari__ subclass)
  * __trout__: several species of fish, annotate as __OOS__ + __no taxid__
  * __leafminer__: insects that eat the tissue of plants, annotate as __OOS__ + __no taxid__
  * __fishes__: __OOS__ (Clade-like concept, non-tetrapoda vertebrata)
  * __bug__: __OOS__
  * __mirid bug__: __Family__+__taxid:30084__ (__Miridae__)
  * __clownfish__: __Family__+__taxid:30863__ (__Pomacentridae__)
  * __elephant__: 3 species, not monophyletic (both __Elephas__ and __Loxodonta__ genera), annotate as __OOS__ + __no taxid__
  * __crab__: infraorder containing 850 species, so it should be annotated as __Order__ + __taxid:6752__ (__Brachyura__)
* Common names that should be annotated in the species level (but could be annotated in a higher taxonomic level)
  * __rat__: synonym for _Rattus norvegicus_ and __Rattus__. Should be annotated as _Rattus norvegicus_ (__taxid:10116__), unless explicitly referring to a different taxonomic unit (e.g. __cotton rat__: __Genus__ + __taxid:42414__ (__Sigmodon__))
  * __fruit fly__: synonym for _Drosophila melanogaster_ and __Drosophila__ genus and __Tephritidae__ family. Should be annotated as _Drosophila melanogaster_ (__taxid:7227__), unless explicitly referring to a different taxonomic unit
  * __bee__: synonym for _Apis mellifera_, and __Apoidea__ superfamily. Should be annotated as _Apis mellifera_ (__taxid:7460__), unless explicitly referring to a different taxonomic unit (e.g. __bumble bee__)
  * __duck__: synonym for _Anas platyrhynchos_, but can be a synonym for other __Anatidae__. Should be annotated as _Anas platyrhynchos_ (__taxid:8839__), unless explicitly referring to a different taxonomic unit
  * __midge__: synonym for _Chironomus thummi_, but can refer to several species of flies. Should be annotated as _Chironomus thummi_ (__taxid:7154__), unless explicitly referring to a different taxonomic unit

#### Very specific distinctions
* 4 mentions of "astomes" in this document [21398102](http://ann.turkunlp.org:8088/index.xhtml#/S800/21398102?focus=sent~9) are __OOS__
* Astome ciliates in this document [21398102](http://ann.turkunlp.org:8088/index.xhtml#/S800/21398102?focus=T15) are also __OOS__
* But some types of astome ciliates had been established as an order _Astomatida_
* FGSC should not be annotated as it refers to something that's out of scope, namely Fusarium graminearum complex [22004876](http://ann.turkunlp.org:8088/index.xhtml#/S800/22004876?focus=T2)
* Mentions of carnivores in [21323921](http://ann.turkunlp.org:8088/index.xhtml#/S800/21323921) have been annotated as __OOS__, interpreting these to refer generally to meat-eating animals rather than the mammalian order __Carnivora__
* __human__ and __primates__ in a context of __non-human primates__ are annotated as __two mentions__ [21295520](http://ann.turkunlp.org:8088/index.xhtml#/S800/21295520?focus=T9)
* __Dictyoptera__ in [19257902](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/19257902) is a clade including two orders Blattodea (cockroaches) and Mantodea (mantids). This has been annotated as __OOS__ + __taxid:6970__ (_Dictyoptera_ clade)
* __termite__ in [19257902](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/19257902) might be a family __Termitoidae__ (termites), even though that's no rank in NCBI taxonomy.  Below Blattoidea superfamily, other sister nodes are family. I decided to annotate as __Family__ + __taxid:1912919__ following the taxonomy presented in __Cataloue of life__
* [2435057](http://ann.turkunlp.org:8088/index.xhtml#/S800-extension/2435057) is discussing about retroviruses, but terminology there is quite old (published in 1987). ICTV (International Committee on Taxonomy of Viruses) was used to figure out how those viruses are called/classified in that period tracing its history.

### Guidelines pending to be applied/decided upon

* mutant species annotation [21054438](http://ann.turkunlp.org:8088/index.xhtml#/S800/21054438?focus=16~22)
* japonica rice in [20946420](http://ann.turkunlp.org:8088/index.xhtml#/S800/20946420?focus=T10)
* phage names [21097633](http://ann.turkunlp.org:8088/index.xhtml#/S800/21097633?focus=93~97)
* biotypes
* plant clones
* vole

## Corpus expansion (S1000)

### Positive classes

* Use UniProt/Swiss-Prot annotations to identify categories of articles aligning with the original S800 categories that mention at least one genus or species that is not already annotated in S800. This will also include all genera of species in the S800 corpus which will be retrieved from mapping of species to their parental ranks in NCBI taxonomy. 
* Process for filtering out "known" species/genera, to get the unique taxids and their corresponding NCBI Taxonomy scientific names from the current iteration of the annotation
<pre><code>
wget 'http://ann.turkunlp.org:8088/ajax.cgi?action=downloadCollection&collection=%2FS800%2F&include_conf=1&protocol=1' -O S800.tar.gz
tar xvzf S800.tar.gz
cat S800/*.ann | egrep '^N' | cut -f 2 | perl -pe 's/^Reference T\d+ Taxonomy:// or die' | sort -n | uniq > unique-taxids.txt
wget ftp://ftp.ncbi.nih.gov/pub/taxonomy/taxdump.tar.gz
tar xvzf taxdump.tar.gz
cut -f 1,3,7 names.dmp | egrep $'\t''scientific name' | cut -f 1,2 > scientific_names.tsv
cut -f 1,5 nodes.dmp > ranks.tsv
paste scientific_names.tsv ranks.tsv | cut -f 1,2,4 > scientific_names_and_ranks.tsv
egrep '('$(tr '\n' '|' < unique-taxids.txt | perl -pe 's/\|$//')')'$'\t' scientific_names_and_ranks.tsv > unique_annotated_names_and_ranks.tsv
</code></pre>
* Four taxids were in the data that were not found in this release of the taxonomy 27380, 67004, 891394, and 891400. These have been included in the final list (`unique_annotated_names_and_ranks.tsv`)
* Filter down to species and genus
<pre><code>
#get species and genera from that list
egrep "species$|genus$" unique_annotated_names_and_ranks.tsv > unique_annotated_names_and_ranks_only_species_genus.tsv
#get species mentions from the above list
egrep "species$" unique_annotated_names_and_ranks.tsv > unique_annotated_names_and_ranks_only_species.tsv
</code></pre>
* The mapping between categories in [S800 publication](https://journals.plos.org/plosone/article/file?id=10.1371/journal.pone.0065390&type=printable) and NCBI Taxonomy
<pre><code>
Category	    NCBI Taxonomy Name (NCBI TaxID)
Protistology	All eukaryotes that are not Metazoa (includes Insects), Fungi* and Plants**
Entomology	  Insecta (50557) - Rank: Class
Virology	    Viruses (10239) - Rank: Superkingdom
Bacteriology	Bacteria (2) - Rank: Superkingdom
Zoology	     Metazoa (33208) excluding those of Class Insecta (50557) - Rank: Kingdom
Mycology	    Fungi (4751) - Rank: Kingdom
Botany	      Viridiplantae (33090) - Rank: Kingdom
* All organisms of the clade Opisthokonta, apart from Metazoa and Fungi, are treated as Protists.
** Chlorophyta and Streptophyta are phyla of Viridiplantae, so they would go to Botany and not to Protists. 
</code></pre>
* Perl scripts and results are on Puhti `/scratch/project_2001426/stringdata/week_39`
<pre><code>
wget ftp://ftp.ncbi.nih.gov/pub/taxonomy/taxdump.tar.gz
tar xvzf taxdump.tar.gz
cut -f1,3,5 nodes.dmp > id_parent_type.dmp
cut -f1,3,7 names.dmp | egrep $'\t''scientific name' | cut -f 1,2 > scientific_names.tsv
paste id_parent_type.dmp scientific_names.tsv > id_parent_type_with_name.tsv
awk -F"\t" 'NR==FNR{a[$1];next}{if($1 in a){print $0}}' unique_annotated_names_and_ranks_only_species.tsv id_parent_type_with_name.tsv> species_in_s800.tsv
cut -f2 species_in_s800.tsv > parents_of_species_in_s800.tsv
awk -F"\t" 'NR==FNR{a[$1];next}{if($1 in a && $3=="genus"){print $0}}' parents_of_species_in_s800.tsv id_parent_type_with_name.tsv> genera_names.tsv
awk -F"\t" '{printf("%s\t%s\t%s\n", $1,$5,$3)}' genera_names.tsv > genera_of_species_in_s800.tsv
#add this in unique_annotated_names_and_ranks_only_species_genus.tsv
cat unique_annotated_names_and_ranks_only_species_genus.tsv genera_of_species_in_s800.tsv > unique_annotated_species_genus_and_species_genera.tsv
sort -u unique_annotated_species_genus_and_species_genera.tsv > tmp && mv tmp unique_annotated_species_genus_and_species_genera.tsv
#get uniprot text file
wget ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.dat.gz
gzip -d uniprot_sprot.dat.gz
#run perl script to generate file with PMIDs per organism
#Input files: unique_annotated_species_genus_and_species_genera.tsv, uniprot_sprot.dat
perl get_organisms_from_swissprot.pl
#Output files: uniprot_organisms_fields_papers.tsv
#get results per genus
cut -f2-4 uniprot_organisms_fields_papers.tsv > uniprot_species-taxid_category_PMIDs.tsv
sort -u uniprot_species-taxid_category_PMIDs.tsv > tmp && mv tmp uniprot_species-taxid_category_PMIDs.tsv
awk -F"\t" 'NR==FNR{a[$1]=$2;next}{if($1 in a){printf("%s\t%s\n",a[$1],$0)}}' id_parent_type_with_name.tsv uniprot_species-taxid_category_PMIDs.tsv > uniprot_parent_taxid_species-taxid_category_PMIDs.tsv
sort -u  uniprot_parent_taxid_species-taxid_category_PMIDs.tsv > tmp && mv tmp  uniprot_parent_taxid_species-taxid_category_PMIDs.tsv
#run perl scripts to get final list:
perl group_by_genera.pl
perl get_unique_elements_last_column.pl
</code></pre>

### Negative class

* Use the tagger outputs to sample articles in which (presumably) no species mentions occur and then tag them with a recent model to assess whether the models have a tendency to overtag due to the training data being enriched for species mentions (by comparison to a random sample of PubMed)
* Create a sample of PMIDs without organism mentions on puhti here: `/scratch/project_2001426/stringdata/week_31_2/no-organism-mention-docs/no-organism-mention-pmid-sample.txt`. 
* Process:
<pre><code>
sinteractive -A project_2001426    # not on a login node!
cd /scratch/project_2001426/stringdata/week_31_2
mkdir no-organism-mention-docs
cd no-organism-mention-docs
cut -f 1,7 ../all_matches.tsv | egrep $'\t''-2$' | cut -f 1 | uniq > organism-mention-pmids.txt
cut -f 1 ../database_documents.tsv > all-pmids.txt
split -l 1000000 all-pmids.txt all-pmids-
for f in all-pmids-*; do echo $f; sort $f > sorted-$f; done
sort -m sorted-all-pmids-* > sorted-all-pmids.txt
rm all-pmids-* sorted-all-pmids-*
split -l 1000000 organism-mention-pmids.txt organism-mention-pmids-
for f in organism-mention-pmids-*; do echo $f; sort $f > sorted-$f; done     
sort -m sorted-organism-mention-pmids-* > sorted-organism-mention-pmids.txt
rm organism-mention-pmids-* sorted-organism-mention-pmids-*
comm -2 -3 sorted-all-pmids.txt sorted-organism-mention-pmids.txt > no-organism-mention-pmids.txt
wc -l all-pmids.txt organism-mention-pmids.txt no-organism-mention-pmids.txt 
 31257225 all-pmids.txt
 10936532 organism-mention-pmids.txt
 20320693 no-organism-mention-pmids.txt
</code></pre>
* i.e. 2/3 have no organism mentions detected by the tagger(!)
<pre><code>
perl -pe '$_ = "" unless(rand()<0.001)' no-organism-mention-pmids.txt > no-organism-mention-pmid-sample.txt
wc -l no-organism-mention-pmid-sample.txt
20406 no-organism-mention-pmid-sample.txt
</code></pre>

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
