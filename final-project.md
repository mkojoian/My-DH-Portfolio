---
layout: page
title: Final Project
permalink: /final-project/
---
Michaiah E. Kojoian
10 December 2022

Final Project

The purpose of this project is to better make the poetry of Gaius Valerius Catullus more accessible both to less confident translators who may need visual assistance to aid their comprehension of the Latin grammar, and to confident Latin readers who are interested in studying the exact relationships between and among words, phrases, and clauses of poetry (although treebanking can be used to better understand prose as well).  For this project I use PERSEIDS, which allows a user to easily create treebanks, text alignments, transcriptions et cētera.  Of Catullus’ poems or carmina, I have chosen as a dataset Catullus’ poems 1 and 49 to fully treebank (i.e. identified both morphology and syntax) with the goal of shedding light on the more nuanced aspects of Catullan poetry.    
Catullus’ first carmen is a dedicatory poem addressed to his friend Cornelius Nepos and reads as follows:  

>cui dono lepidum novum libellum
>arida modo pumice expolitum?
>Corneli, tibi! namque tu solebas
>meas esse aliquid putare nugas,
>iam tum cum ausus es unus Italorum
>omne aevum tribus explicare cartis,
>doctis Iuppiter et laboriosis.
>quare habe tibi quidquid hoc libelli.
>qualecumque quod, o patrona virgo,
>plus uno maneat perenne saeclo. (Catullus, Carmen 1)

>“To whom do I give (this) charming new little book recently polished with a dry pumice stone?  To you, Cornelius!  For you were accustomed to think my trifles were something.  Now then, when the only one of the Italians dared to explain all history on three papyri, learned, by Jupiter, and with much labor.  Therefore, have for yourself this thing whatever it (is) of a little book.  Which whatever (it is) O virgin patron, may it remain for more than one age.” 

[xml markup](https://mkojoian.github.io/My-DH-Portfolio/exercises/Catullus_Poem_1.html)

As is apparent from the words enclosed within parentheses, some words must be added for a grammatical and understandable English translation.  More importantly however, there are also Latin words that must be added to the xml—words that were not penned by Catullus—in order to fully understand the dependency grammar that is implied.  For instance, the second sentence of the above poem “Corneli, tibi” (“to you Cornelius”) contains no verb, making it necessary to add an additional line of code to the xml upon which the proper noun Corneli and the pronoun tibi can grammatically depend.  A Roman audience would have understood the verb of this sentence to be the same as the verb of the main verb of the previous sentence (i.e. dono).  The sentence would thus be understood “I give it to you Cornelius.”  Because the reapplication of the previous verb was intuitive to his audience, Catullus chose not to repeat it in this second sentence.   This being the case, in order to treebank this sentence, a verb does need to be supplied.  
The process for inserting an additional word into the xml is fairly straightforward and is highlighted in red below.
```
  </sentence>
   <sentence id="2"
             document_id="urn:cts:latinLit:phi0472.phi001.perseus-lat2"
             subdoc="1.1-1.10"
             span="">
      <word id="1" form="Corneli" lemma="" postag="" relation="" head=""/>
      <word id="2" form="," lemma="punc1" postag="u--------" relation="AuxX" head="0"/>
      <word id="3" form="tibi" lemma="" postag="" relation="" head=""/>
      <word id="4" form=";" lemma="punc1" postag="u--------" relation="AuxK" head="0"/>
      <word id="5" insertion_id="0003e" artificial="elliptic" relation="head" form="(dono)" head="0"/>
   </sentence>
   ```

Each sentence is enclosed within two sentence delimiters ```</sentence>``` and within this structure, each sentence has a unique id number.  Each word (punctuation marks included) in turn is assigned a word id and the appropriate initial “tag” identifying the dictionary form of the word from which it came.   In this case, dono was added as the verb and effectively becomes the fifth word (word id= “5”) in the sentence.  It is important to note that the postag and relation are still unidentified (as is apparent from the “” with no descriptive tagging).  Tasks such as this cannot be completed by a computer and require a user to determine the syntactical relationships within the sentence.  After this process is completed, these fields too are descriptive (see the highlighted section below in which Cornelius is marked as a singular masculine noun and its relation is vocative) with the xml appearing as below:
```
</sentence>
   <sentence id="2" document_id=" urn:cts:latinLit:phi0472.phi001.perseus-lat2" subdoc="1.1-1.10" span="">
      <word id="1" form="Corneli" lemma="Cornelius" postag="n-s---mv-" relation="V-VOC" head="5"/>
      <word id="2" form="," lemma="punc1" postag="u--------" relation="AuxX" head="1"/>
      <word id="3" form="tibi" lemma="tu" postag="p-s---md-" relation="D-IO" head="5"/>
      <word id="4" form=":" lemma="punc1" postag="u--------" relation="AuxK" head="0"/>
      <word id="5" insertion_id="0003e" artificial="elliptic" relation="PRED" form="(dono)" head="0"/>
   </sentence>
```
While these changes may appear rather unimpressive in the xml, the visual this code represents is a completely treebanked sentence with the vocative noun appearing in light blue (the color associated with nouns) and the pronoun appearing in purple (the color associated with pronouns).  Note that the verb is in black and not in its customary red (the color associated with verb forms).  This is because this word was added manually and not original to the poem.      

![Catullus poem 1](../assets/Image 1.png)

Continuing to edit this poem for final treebanking, the next sentence includes the enclitic -que (translated as “and” and attached to the end of the preceding word nam).  Because the treebank requires the elements of a given sentence to function independently,  the -que must be split from the nam so that both can individually function properly within the sentence.  The xml copied below does not represent a text ready to be treebanked since nam and -que are currently not able to function independently when grouped together as a single word unit  and must therefore be split. 
```
<sentence id="3"
             document_id="urn:cts:latinLit:phi0472.phi001.perseus-lat2"
             subdoc="1.1-1.10"
             span="">
      <word id="1" form="namque" lemma="" postag="" relation="" head=""/>
      <word id="2" form="tu" lemma="" postag="" relation="" head=""/>
.				.				.			
.				.				.
.				.				.

</sentence> 
```
It should be noted that the necessity of this change is merely a convention of representing this sentence visually (in a treebank) and not at all a departure from the way the Romans would have understood the grammar of this sentence.  To represent this change for treebanking readability, the new tokenization should appear thus: 
```
</sentence>
   <sentence id="3" document_id="" subdoc="" span="">
      <word id="1" form="nam" lemma="nam" postag="c--------" relation="AuxY" head="4"/>
      <word id="2" form="-que" lemma="-que" postag="c--------" relation="COORD" head="0"/>
      <word id="3" form="tu" lemma="tu" postag="p-s---mn-" relation="N-SUBJ" head="4"/>  
.				.				.			
.				.				.
.				.				.

</sentence>
```
As seen from the xml above, the nam can now receive the postag AuxY, indicating that it is a sentence adverbial while the -que receives the postag COORD, indicating that it is a coordinator.  
Though a very common example of how textual data must be “cleaned” before it can be treebanked, enclitics are not the only case in which words will need to be split.  Sometimes tokenizing errors occur in which two completely independent words are incorrectly interpreted as a single word unit.  An example of this is shown in the xml below in which the verb solebas and the accusative pronoun meas were incorrectly interpreted as one word by the xml parser.     
```
</sentence>
   <sentence id="3"
             document_id="urn:cts:latinLit:phi0472.phi001.perseus-lat2"
             subdoc="1.1-1.10"
             span="">
      <word id="1" form="nam" lemma="" postag="" relation="" head=""/>
      <word id="2" form="-que" lemma="" postag="" relation="" head=""/>
      <word id="3" form="tu" lemma="" postag="" relation="" head=""/>
      <word id="4" form="solebasmeas" lemma="" postag="" relation="" head=""/>
.				.				.			
.				.				.
.				.				.

</sentence>
```
As it was necessary to separate namque above, it is also necessary to separate these two words (i.e. give each its own word id) so that each can function independently in the sentence.  Below is the corrected form: 
```
</sentence>
   <sentence id="3"
             document_id="urn:cts:latinLit:phi0472.phi001.perseus-lat2"
             subdoc="1.1-1.10"
             span="">
      <word id="1" form="nam" lemma="" postag="" relation="" head=""/>
      <word id="2" form="-que" lemma="" postag="" relation="" head=""/>
      <word id="3" form="tu" lemma="" postag="" relation="" head=""/>
      <word id="4" form="solebas" lemma="" postag="" relation="" head=""/>
      <word id="5" form="meas" lemma="" postag="" relation="" head=""/>
.				.				.			
.				.				.
.				.				.

</sentence>
```
At this point, this poem is almost ready to be fully treebanked.  The final issue lies in the last sentence in which the words patrona and virgo are both vocatives dependent on maneat and in apposition to one another, meaning that they are equal (neither one is dependent upon—i.e. modifying the other).  In addition, the interjection “o”  belongs to both patrona and virgo equally, so making it depend on either one or the other would misrepresent Catullus’ meaning.  In this case, an appositional element must be added (represented by [ ]) upon which both words hang.  The interjection “o” will also hang from this newly created appositional element so that it is visually clear that the interjection should be associated with both nouns equally.  The xml and associated treebank are below: 
```
</sentence>
   <sentence id="4" document_id="" subdoc="" span="">
.				.				.			
.				.				.
.				.				.

      <word id="15" form="patrona" lemma="patrona" postag="n-s---fv-" relation="V-VOC_AP" head="24"/>
      <word id="16" form="uirgo" lemma="virgo" postag="n-s---fv-" relation="V-VOC_AP" head="24"/>
.				.				.			
.				.				.
.				.				.

      <word id="24" insertion_id="0015e" artificial="elliptic" relation="APOS" form="[ ]" head="20"/>
 </sentence>
```
![Catullus poem 1](../assets/Image 2.png)

While the data collection and editing processes can be rather tedious, for seemingly unimpressive results, treebanking itself is not always straightforward and the process can lead to new ideas about the different meanings invested in these poems.  As previously mentioned, treebanking technology is useful not only for those hoping to visualize the basic grammar, but also for those interested in a more advanced analysis of poetry and prose.  Treebanking poem forty-nine, for example, provides an interesting example of the way Catullus manipulates the Latin language with a double entendre that would not have been lost on his ancient audience:  

>Disertissime Romuli nepotum,
>quot sunt quotque fuere, Marce Tulli,
>quotque post aliis erunt in annis,
>gratias tibi maximas Catullus
>agit pessimus omnium poeta,
>tanto pessimus omnium poeta,
>quanto tu optimus omnium patronus. (Catullus, Carmina 49).

>Most eloquent son of Romulus how many there are, and how many have been, and how many there will be afterwards in other years, Marcus Tullius, Catullus gives the greatest thanks to you as the worst of all poets, by far the worst of all poets, as much as you are the best patron of all. 

[xml markup](https://mkojoian.github.io/My-DH-Portfolio/exercises/Catullus_Poem_49.html)

The repetitive language in this poem is apparent from the very beginning in which, after stating that Cicero (Marcus Tullius) is the most eloquent poet, Catullus goes on to specify that he is the best poet of the past, the present, and (as he predicts) the future.  This is redundant as this meaning is included within the superlative adjective disertissime.  Also significant are the apparent redundancies Catullus exercises throughout the poem.  Distinguishing Cicero as the most eloquent son of Romulus would have been sufficient without the elaborate temporal specification of past, present, and future (with the triple repetition of quot) which entirely consumes the entirety of the second and third lines.
Another redundancy is the wordplay on omnium in the last three lines.  In all three cases, omnium is an attributive adjective modifying poeta (or patronus in the case of the last line).  The question, then, becomes what particular use of the genitive these are.  They could easily be understood as the partitive genitive implying that Catullus was humbly making a comparison between himself (the worst of all poets) and Cicero (the best of all patrons).  This would be consistent with the opening lines of the poem in which Cicero was described as the most eloquent (disertissime) of Romulus’ sons.  But there is another possibility: the repeated genitive omnium could also reasonably be marked as a possessive genitive (i.e. “the best patron of everyone”) in a gesture of mockery.  It was well known that, in 56 BC, Cicero defended Marcus Caelius Rufus, who had formerly been his political rival.  Again in 54 he famously defended Vatinius, whom he had previously prosecuted.  Because of these incidents, the orator’s well-known ability to speak either on behalf of or against whomever he pleased would have made this an obvious (and humorous) alternative interpretation for Catullus’ listeners.   
While it would certainly be possible to consider nuances such as the different possible case-usages omnium could be representing in this poem, being forced to establish a unidirectional line of modification necessary when creating a treebank forces the reader to think about the exact relationship between words and between clauses, different morphological and syntactical possibilities, and ultimately, how this information can be synthesized for the most accurate textual interpretation.  While treebanking may not directly provide the answer to every question one may have about a text, it certainly avails the user of a closer, more detail-oriented reading, making it a valuable tool both for those interested in determining basic grammatical structure as well as those who hope to gain a more in-depth historical and philological analysis of these poems by combining a traditional close reading approach with the digital tools made possible by xml.  

BIBLIOGRAPHY:
Treebanking sources: 
Hajič Jan, Eva Hajičová, Marie Mikulová, and Jiří Mírovský. “Prague Dependency Treebank.” Handbook of Linguistic Annotation, 2017, 555–94. https://doi.org/10.1007/978-94-024-0881-2_21. 
Bamman David, Passarotti Marco, Crane Gregory and Raynaud Savina “Guidelines for the syntactic annotation of Latin treebanks (v. 1.3),” Tufts University, Boston, 2007.
Kübler Sandra, Nivre Joakim, Hinrichs Erhard, and Wunsch Holger. “Proceedings of the Third	Workshop on Treebanks and Linguistic Theories (TLT),” 2004. 
Hajič Jan, Hladka Barbora, and Pajas Petr. “The Prague Dependency Treebank: Annotation 
Structure and Support.” 2001, 105-114.

Harrington J. Matthew. “Latin Treebanking Instructions 3.0.” 2021. 

Poetry sources:
Bodoh, John J. “Catullus 58.” L’Antiquité Classique 45, no. 2 (1976): 627–29.

Cairns, Francis. “Catullus I.” Mnemosyne 22, no. 2 (1969): 153–58.

Catullus. A Companion to Catullus. Edited by Marilyn B. Skinner. Malden, MA: Blackwell Publishing Ltd., 2007.
———. Catullus. Edited by C.J. Fordyce. London: Oxford University Press, 1961.
———. Catullus the Poems. Edited by Kenneth Quinn. London: Bristol Classical Press, 1996.
———. The Student’s Catullus. Edited by Daniel H. Garrison. 4th ed. Norman, Oklahoma: University of Oklahoma Press, 1989.

Colby, John Kingsbury. “‘Catullus’ 1.7.” The Classical Weekly 19, no. 15 (February 15, 1926): 123.

Collins, John H. “Cicero and Catullus.” The Classical Journal 48, no. 1 (October 1952): 11-17, 36-41.

Copley, Frank O. “Catullus, c. 1.” Transactions and Proceedings of the American Philological Association 82 (1951): 200–206.

Ellis, Robinson. A Commentary on Catullus. 2nd ed. London: Oxford University Press, 1889.

Gibson, B. J. “Catullus 1.5-7.” The Classical Quarterly 45, no. 2 (1995): 569–73.

Harrington, Karl Pomeroy. Catullus and His Influence. Boston, MA: Marshall Jones Company, 1923.

Krostenko, Brian A. “Catullus and Elite Republican Social Discourse.” In A Companion to Catullus, edited by Marilyn B. Skinner, 212–32. Malden, MA: Blackwell Publishing Ltd., 2007.

Martin, Charles. Catullus. London: Yale University Press, 1992.


Svavarsson, Svavar Hrafn. “On Catullus 49.” The Classical Journal 95, no. 2 (December 1999): 131–38.

Tatum, W. Jeffrey. “Catullus’ Criticism of Cicero in Poem 49.” Transactions of the American Philological Association 118 (1988): 179–84.
———. “Friendship, Politics, and Literature in Catullus: Poems 1, 65 and 66, 116.” The Classical Quarterly 47, no. 2 (1997): 482–500.

