---
layout: page
title: Named Entity Recognition
description: The HW is at the end of this notebook.  Here I use NER on chapter 38 of Gibbon's Decline and Fall of the Roman Empire.  
---

# Named Entity Recognition (NER)

Named entity recognition (NER) is a branch of natural language processing that focuses on extracting the text of names or other semantically distinct ideas from a larger text and tagging according to its special meaning within a system. NER can be broken into two broad categories: algorithms which use deterministic rules to find names (find all tokens that match the regular expression: `([A-Z][a-z]+)`) and statisical models which make guesses about where an entity begins and ends.

In this class, we'll use `spaCy`'s small English NER (`en_core_web_sm`) model to explore how statistical models can recognize named entities. Then, we'll see some of the practical considerations we must take when working with NER. Our workflow will look like this:
* Reacquaint ourselves with `spaCy`
* Pass a single chapter of Gibbon's *Decline and Fall* into the `spaCy` parser
* Examine the NER results
* Filter out all place names
* Use the Pleiades gazetteer to get coordinates for all valid place names
* Save data to CSV

Finally, you'll have the chance to boil down our process into a function and test it out on other chapters from Gibbon (or other texts). Keep in mind that, for next class, you must have your own version of the data we produce today. That's because, next week, Carolyn Talmadge from the DataLab will be showing us how to turn our CSVs into webmaps and applications. 

## Preparing our data


```python
# redownload spaCy's small model - should see 'requirement already satisfied'
!python -m spacy download en_core_web_sm
```

    Collecting en-core-web-sm==3.4.1
      Downloading https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-3.4.1/en_core_web_sm-3.4.1-py3-none-any.whl (12.8 MB)
    [K     |████████████████████████████████| 12.8 MB 1.0 MB/s eta 0:00:01    |██████████████▌                 | 5.8 MB 3.2 MB/s eta 0:00:03
    [?25hRequirement already satisfied: spacy<3.5.0,>=3.4.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from en-core-web-sm==3.4.1) (3.4.1)
    Requirement already satisfied: catalogue<2.1.0,>=2.0.6 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.8)
    Requirement already satisfied: pydantic!=1.8,!=1.8.1,<1.10.0,>=1.7.4 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.9.2)
    Requirement already satisfied: packaging>=20.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (21.3)
    Requirement already satisfied: spacy-loggers<2.0.0,>=1.0.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.0.3)
    Requirement already satisfied: requests<3.0.0,>=2.13.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.27.1)
    Requirement already satisfied: murmurhash<1.1.0,>=0.28.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.0.8)
    Requirement already satisfied: spacy-legacy<3.1.0,>=3.0.9 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.0.10)
    Requirement already satisfied: typer<0.5.0,>=0.3.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.4.2)
    Requirement already satisfied: pathy>=0.3.5 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.6.2)
    Requirement already satisfied: preshed<3.1.0,>=3.0.2 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.0.7)
    Requirement already satisfied: langcodes<4.0.0,>=3.2.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.3.0)
    Requirement already satisfied: wasabi<1.1.0,>=0.9.1 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.10.1)
    Requirement already satisfied: srsly<3.0.0,>=2.4.3 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.4.4)
    Requirement already satisfied: tqdm<5.0.0,>=4.38.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (4.64.0)
    Requirement already satisfied: numpy>=1.15.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.21.5)
    Requirement already satisfied: setuptools in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (61.2.0)
    Requirement already satisfied: jinja2 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.11.3)
    Requirement already satisfied: cymem<2.1.0,>=2.0.2 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.6)
    Requirement already satisfied: thinc<8.2.0,>=8.1.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (8.1.2)
    Requirement already satisfied: pyparsing!=3.0.5,>=2.0.2 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from packaging>=20.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.0.4)
    Requirement already satisfied: smart-open<6.0.0,>=5.2.1 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from pathy>=0.3.5->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (5.2.1)
    Requirement already satisfied: typing-extensions>=3.7.4.3 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from pydantic!=1.8,!=1.8.1,<1.10.0,>=1.7.4->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (4.1.1)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.26.9)
    Requirement already satisfied: certifi>=2017.4.17 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2021.10.8)
    Requirement already satisfied: charset-normalizer~=2.0.0 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.4)
    Requirement already satisfied: idna<4,>=2.5 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.3)
    Requirement already satisfied: confection<1.0.0,>=0.0.1 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from thinc<8.2.0,>=8.1.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.0.2)
    Requirement already satisfied: blis<0.8.0,>=0.7.8 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from thinc<8.2.0,>=8.1.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.7.8)
    Requirement already satisfied: click<9.0.0,>=7.1.1 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from typer<0.5.0,>=0.3.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (8.0.4)
    Requirement already satisfied: MarkupSafe>=0.23 in /Users/michaiahkojoian/opt/anaconda3/lib/python3.9/site-packages (from jinja2->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.1)
    [38;5;2m✔ Download and installation successful[0m
    You can now load the package via spacy.load('en_core_web_sm')



```python
import pandas as pd
import spacy
nlp = spacy.load('en_core_web_sm') # good idea to initialize here
```


```python
# downloading gibbon text from my gh
import wget
import os
if not os.path.isfile('gibbon_text.csv'):
    wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/gibbon_text.csv')
```


```python
gibbon_by_chapter = pd.read_csv('gibbon_text.csv').rename(columns={'Unnamed: 0':'chapter'})
gibbon_by_chapter
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>chapter</th>
      <th>StringText</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Chapter 1</td>
      <td>\nIn the second century of the Christian era, ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Chapter 2</td>
      <td>\nIt is not alone by the rapidity or extent of...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Chapter 3</td>
      <td>\nThe obvious definition of a monarchy seems t...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Chapter 4</td>
      <td>\nThe mildness of Marcus, which the rigid disc...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Chapter 5</td>
      <td>\nThe power of the sword is more sensibly felt...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Chapter 67</td>
      <td>\nThe respective merits of Rome and Constantin...</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Chapter 68</td>
      <td>\nThe siege of Constantinople by the Turks att...</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Chapter 69</td>
      <td>\nIn the first ages of the decline and fall of...</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Chapter 70</td>
      <td>\nIn the apprehension of modern times, Petrarc...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Chapter 71</td>
      <td>\nIn the last days of Pope Eugenius the Fourth...</td>
    </tr>
  </tbody>
</table>
<p>71 rows × 2 columns</p>
</div>




```python
gibbon_by_chapter['StringText'][70]
```




    "\nIn the last days of Pope Eugenius the Fourth, two of his servants, the learned Poggius and a friend, ascended the Capitoline Hill; reposed themselves among the ruins of columns and temples; and viewed, from that commanding spot, the wide and various prospect of desolation. The place and the object gave ample scope for moralising on the vicissitudes of fortune, which spares neither man nor the proudest of his works, which buries empires and cities in a common grave; and it was agreed that in proportion to her former greatness the fall of Rome was the more awful and deplorable. Her primeval state, such as she might appear in a remote age, when Evander entertained the stranger of Troy, has  been delineated by the fancy of Virgil. This Tarpeian rock was then a savage and solitary thicket: in the time of the poet, it was crowned with the golden roofs of a temple: the temple is overthrown, the gold has been pillaged, the wheel of fortune has accomplished her revolution, and the sacred ground is again disfigured with thorns and brambles. The hill of the Capitol, on which we sit, was formerly the head of the Roman empire, the citadel of the earth, the terror of kings; illustrated by the footsteps of so many triumphs, enriched with the spoils and tributes of so many nations. This spectacle of the world, how is it fallen! how changed! how defaced! The path of victory is obliterated by vines, and the benches of the senators are concealed by a dunghill. Cast your eyes on the Palatine Hill, and seek, among the shapeless and enormous fragments, the marble theatre, the obelisks, the colossal statues, the porticoes of Nero's palace: survey the other hills of the city, the vacant space is interrupted only by ruins and gardens. The forum of the Roman people, where they assembled to enact their laws and elect their magistrates, is now enclosed for the cultivation of pot-herbs or thrown open for the reception of swine and buffaloes. The public and private edifices, that were founded for eternity, lie prostrate, naked, and broken, like the limbs of a mighty giant; and the ruin is the more visible, from the stupendous relics that have survived the injuries of time and fortune.\nThese relics are minutely described by Poggius, one of the first who raised his eyes from the monuments of legendary, to those of classic, superstition. 1. Besides a bridge, an arch, a  sepulchre, and the pyramid of Cestius, he could discern, of the age of the republic, a double row of vaults in the salt-office of the Capitol, which were inscribed with the name and munificence of Catulus. 2. Eleven temples were visible in some degree, from the perfect form of the Pantheon, to the three arches and a marble column of the temple of Peace, which Vespasian erected after the civil wars and the Jewish triumph. 3. Of the number, which he rashly defines, of seven thermae, or public baths, none were sufficiently entire to represent the use and distribution of the several parts; but those of Diocletian and Antoninus Caracalla still retained the titles of the founders, and astonished the curious spectator, who, in observing their solidity and extent, the variety of marbles, the size and multitude of the columns, compared the labour and expense with the use and importance. Of the baths of Constantine, of Alexander, of Domitian, or rather of Titus, some vestige might yet be found. 4. The triumphal arches of Titus, Severus, and Constantine were entire, both the structure and the inscriptions; a falling fragment was honoured with the name of Trajan; and two arches, then extant in the Flaminian Way, have been ascribed to the baser memory of Faustina and Gallienus. 5. After the wonder of the  Coliseum, Poggius might have overlooked a small amphitheatre of brick, most probably for the use of the praetorian camp. The theatres of Marcellus and Pompey were occupied, in a great measure, by public and private buildings; and in the circus, Agonalis and Maximus, little more than the situation and the form could be investigated. 6. The columns of Trajan and Antonine were still erect; but the Egyptian obelisks were broken or buried. A people of gods and heroes, the workmanship of art, was reduced to one equestrian figure of gilt brass, and to five marble statues, of which the most conspicuous were the two horses of Phidias and Praxiteles. 7. The two mausoleums or sepulchres of Augustus and Hadrian could not totally be lost; but the former was only visible as a mound of earth; and the latter, the castle of St. Angelo, had acquired the name and appearance of a modern fortress. With the addition of some separate and nameless  columns, such were the remains of the ancient city; for the marks of a more recent structure might be detected in the walls, which formed a circumference of ten miles, included three hundred and seventy-nine turrets, and opened into the country by thirteen gates.\nThis melancholy picture was drawn above nine hundred years after the fall of the Western empire, and even of the Gothic kingdom of Italy. A long period of distress and anarchy, in which empire, and arts, and riches had migrated from the banks of the Tiber, was incapable of restoring or adorning the city; and, as all that is human must retrograde if it do not advance, every successive age must have hastened the ruin of the works of antiquity. To measure the progress of decay, and to ascertain, at each era, the state of each edifice, would be an endless and a useless labour; and I shall content myself with two observations, which will introduce a short inquiry into the general causes and effects. 1. Two hundred years before the eloquent complaint of Poggius, an anonymous writer composed a description of Rome. His ignorance may repeat the same objects under strange and fabulous names. Yet this Barbarous topographer had eyes and ears: he could observe the visible remains; he could  listen to the tradition of the people; and he distinctly enumerates seven theatres, eleven baths, twelve arches, and eighteen palaces, of which many had disappeared before the time of Poggius. It is apparent that many stately monuments of antiquity survived till a late period, and that the principles of destruction acted with vigorous and increasing energy in the thirteenth and fourteenth centuries. 2. The same reflection must be applied to the three last ages; and we should vainly seek the Septizonium of Severus, which is celebrated by Petrarch and the antiquarians of the sixteenth century. While the Roman edifices were still entire, the first blows, however weighty and impetuous, were resisted by the solidity of the mass and the harmony of the parts; but the slightest touch would precipitate the fragments of arches and columns that already nodded to their fall.\nAfter a diligent inquiry, I can discern four principal causes of the ruin of Rome, which continued to operate in a period of more than a thousand years. I. The injuries of time and nature. II. The hostile attacks of the Barbarians and Christians. III. The use and abuse of the materials. And, IV. The domestic quarrels of the Romans.\nI. The art of man is able to construct monuments far more permanent than the narrow span of his own existence; yet these monuments, like himself, are perishable and frail; and, in the boundless annals of time, his life and his labours must  equally be measured as a fleeting moment. Of a simple and solid edifice, it is not easy, however, to circumscribe the duration. As the wonders of ancient days, the pyramids attracted the curiosity of the ancients: an hundred generations, the leaves of autumn, have dropped into the grave; and, after the fall of the Pharaohs and Ptolemies, the Caesars and Caliphs, the same pyramids stand erect and unshaken above the floods of the Nile. A complex figure of various and minute parts is more accessible to injury and decay; and the silent lapse of time is often accelerated by hurricanes and earthquakes, by fires and inundations. The air and earth have doubtless been shaken; and the lofty turrets of Rome have tottered from their foundations; but the seven hills do not appear to be placed on the great cavities of the globe; nor has the city, in any age, been exposed to the convulsions of nature which, in the climate of Antioch, Lisbon, or Lima, have crumbled in a few moments the works of ages into dust. Fire is the most powerful agent of life and death: the rapid mischief may be kindled and propagated by the industry or negligence of mankind; and every period of the Roman annals is marked by the repetition of similar calamities. A memorable conflagration, the guilt or misfortune of Nero's reign, continued, though with unequal fury, either six or nine days. Innumerable buildings, crowded in close and  crooked streets, supplied perpetual fuel for the flames; and, when they ceased, four only of the fourteen regions were left entire; three were totally destroyed, and seven were deformed by the relics of smoking and lacerated edifices. In the full meridian of empire, the metropolis arose with fresh beauty from her ashes; yet the memory of the old deplored their irreparable losses, the arts of Greece, the trophies of victory, the monuments of primitive or fabulous antiquity. In the days of distress and anarchy, every wound is mortal, every fall irretrievable; nor can the damage be restored either by the public care of government or the activity of private interest. Yet two causes may be alleged, which render the calamity of fire more destructive to a flourishing than a decayed city. I. The more combustible materials of brick, timber, and metals are first melted or consumed; but the flames may play without injury or effect on the naked walls and massy arches that have been despoiled of their ornaments. 2. It is among the common and plebeian habitations that a mischievous spark is most easily blown to a conflagration; but, as soon as they are devoured, the greater edifices which have resisted or escaped are left as so many islands in a state of solitude and safety. From her situation, Rome is exposed to the danger of frequent inundations. Without excepting the Tiber, the rivers that descend from either side of the Apennine have a short and irregular course; a shallow stream in the summer heats; an impetuous torrent, when it is swelled  in the spring or winter by the fall of rain and the melting of the snows. When the current is repelled from the sea by adverse winds, when the ordinary bed is inadequate to the weight of waters, they rise above the banks, and overspread, without limits or control, the plains and cities of the adjacent country. Soon after the triumph of the first Punic war, the Tiber was increased by unusual rains; and the inundation, surpassing all former measure of time and place, destroyed all the buildings that were situate below the hills of Rome. According to the variety of ground, the same mischief was produced by different means; and the edifices were either swept away by the sudden impulse, or dissolved and undermined by the long continuance, of the flood. Under the reign of Augustus, the same calamity was renewed: the lawless river overturned the palaces and temples on its banks; and, after the labours of the emperor in cleansing and widening the bed that was encumbered with ruins, the vigilance of his successors was exercised by similar dangers and designs. The project of diverting into new channels the Tiber itself, or some of the dependent streams, was long opposed by  superstition and local interests; nor did the use compensate the toil and cost of the tardy and imperfect execution. The servitude of rivers is the noblest and most important victory which man has obtained over the licentiousness of nature; and, if such were the ravages of the Tiber under a firm and active government, what could oppose, or who can enumerate, the injuries of the city after the fall of the Western empire? A remedy was at length produced by the evil itself: the accumulation of rubbish and the earth that had been washed down from the hills is supposed to have elevated the plain of Rome fourteen or fifteen feet, perhaps, above the ancient level; and the modern city is less accessible to the attacks of the river.\nII. The crowd of writers of every nation, who impute the destruction of the Roman monuments to the Goths and the Christians, have neglected to inquire how far they were animated by an hostile principle and how far they possessed the means and the leisure to satiate their enmity. In the preceding volumes of this History, I have described the triumph of Barbarism and religion; and I can only resume, in a few words, their real or imaginary connection with the ruin of ancient Rome. Our fancy may create, or adopt, a pleasing  romance, that the Goths and Vandals sallied from Scandinavia, ardent to avenge the flight of Odin, to break the chains, and to chastise the oppressors, of mankind; that they wished to burn the records of classic literature and to found their national architecture on the broken members of the Tuscan and Corinthian orders. But, in simple truth, the Northern conquerors were neither sufficiently savage nor sufficiently refined to entertain such aspiring ideas of destruction and revenge. The shepherds of Scythia and Germany had been educated in the armies of the empire, whose discipline they acquired, and whose weakness they invaded; with the familiar use of the Latin tongue, they had learned to reverence the name and titles of Rome; and, though incapable of emulating, they were more inclined to admire than to abolish, the arts and studies of a brighter period. In the transient possession of a rich and unresisting capital, the soldiers of Alaric and Genseric were stimulated by the passions of a victorious army; amidst the wanton indulgence of lust or cruelty, portable weath was the object of their search; nor could they derive either pride or pleasure from the unprofitable reflection that they had battered to the ground the works of the consuls and Caesars. Their moments were indeed precious: the Goths evacuated Rome on the sixth, the Vandals on the fifteenth, day; and, though it be far more difficult to build than to destroy, their hasty assault would have made a slight impression on the solid piles of antiquity. We may remember that both Alaric and Genseric affected to spare the buildings of the city; that they subsisted in strength and beauty under the auspicious government of Theodoric;  and that the momentary resentment of Totila was disarmed by his own temper and the advice of his friends and enemies. From these innocent Barbarians the reproach may be transferred to the Catholics of Rome. The statues, altars, and houses of the demons were an abomination in their eyes; and in the absolute command of the city they might labour with zeal and perseverance to erase the idolatry of their ancestors. The demolition of the temples in the East affords to them an example of conduct, and to us an argument of belief; and it is probable that a portion of guilt or merit may be imputed with justice to the Roman proselytes. Yet their abhorrence was confined to the monuments of heathen superstition; and the civil structures that were dedicated to the business or pleasure of society might be preserved without injury or scandal. The change of religion was accomplished, not by a popular tumult, but by the decrees of the emperor, of the senate, and of time. Of the Christian hierarchy, the bishops of Rome were commonly the most prudent and least fanatic; nor can any positive charge be opposed to the meritorious act of saving and converting the majestic structure of the Pantheon.\nIII. The value of any object that supplies the wants or pleasures of mankind is compounded of its substance and  its form, of the materials and the manufacture. Its price must depend on the number of persons by whom it may be acquired and used; on the extent of the market; and consequently on the ease or difficulty of remote exportation, according to the nature of the commodity, its local situation, and the temporary circumstances of the world. The Barbarian conquerors of Rome usurped in a moment the toil and treasure of successive ages; but, except the luxuries of immediate consumption, they must view without desire all that could not be removed from the city in the Gothic waggons or the fleet of the Vandals. Gold and silver were the first objects of their avarice; as in every country, and in the smallest compass, they represent the most ample command of the industry and possessions of mankind. A vase or a statue of those precious metals might tempt the vanity of some Barbarian chief; but the grosser multitude, regardless of the form, was tenacious only of the substance; and the melted ingots might be readily divided and stamped into the current coin of the empire. The less active or less fortunate robbers were reduced to the baser plunder of brass, lead, iron, and copper; whatever had escaped the Goths and Vandals was pillaged by the Greek tyrants; and the emperor Constans, in his rapacious visit, stripped the bronze tiles from the roof of the Pantheon. The edifices of Rome might be considered as a vast and various mine: the first labour of extracting the materials was already performed; the metals were purified  and cast; the marbles were hewn and polished; and, after foreign and domestic rapine had been satiated, the remains of the city, could a purchaser have been found, were still venal. The monuments of antiquity had been left naked of their precious ornaments, but the Romans would demolish with their own hands the arches and walls, if the hope of profit could surpass the cost of the labour and exportation. If Charlemagne had fixed in Italy the seat of the Western empire, his genius would have aspired to restore, rather than to violate, the works of the Caesars; but policy confined the French monarch to the forests of Germany; his taste could be gratified only by destruction; and the new palace of Aix la Chapelle was decorated with the marbles of Ravenna and Rome. Five hundred years after Charlemagne, a king of Sicily, Robert, the wisest and most liberal sovereign of the age, was supplied with the same materials by the easy navigation of the Tiber and the sea; and Petrarch sighs an indignant complaint that the ancient capital of the world should adorn, from her own bowels, the slothful luxury of Naples. But these examples of plunder or purchase were  rare in the darker ages; and the Romans, alone and unenvied, might have applied to their private or public use the remaining structures of antiquity, if in their present form and situation they had not been useless in a great measure to the city and its inhabitants. The walls still described the old circumference, but the city had descended from the seven hills into the Campus Martius; and some of the noblest monuments which had braved the injuries of time were left in a desert, far remote from the habitations of mankind. The palaces of the senators were no longer adapted to the manners or fortunes of their indigent successors; the use of baths and porticoes was forgotten; in the sixth century, the games of the theatre, amphitheatre, and circus had been interrupted; some temples were devoted to the prevailing worship; but the Christian churches preferred the holy figure of the cross; and fashion or reason had distributed, after a peculiar model, the cells and offices of the cloister. Under the ecclesiastical reign, the number of these pious foundations was enormously multiplied; and the city was crowded with forty monasteries of men, twenty of women, and sixty chapters and colleges of canons and priests, who aggravated, instead of relieving, the depopulation of the tenth century. But, if the forms  of ancient architecture were disregarded by a people insensible of their use and beauty, the plentiful materials were applied to every call of necessity or superstition, till the fairest columns of the Ionic and Corinthian orders, the richest marbles of Paros and Numidia, were degraded, perhaps, to the support of a convent or a stable. The daily havoc which is perpetrated by the Turks in the cities of Greece and Asia may afford a melancholy example; and, in the gradual destruction of the monuments of Rome, Sixtus the Fifth may alone be excused for employing the stones of the Septizonium in the glorious edifice of St. Peter's. A fragment, a ruin, howsoever mangled or profaned, may be viewed with pleasure and regret; but the greater part of the marble was deprived of substance, as well as of place and proportion; it was burnt to lime for the purpose of cement. Since the arrival of Poggius, the temple of Concord and many capital structures had vanished from his eyes; and an epigram of the same age expresses a just and pious fear that the continuance of this practice would finally annihilate all the monuments of antiquity. The smallness of their numbers was the sole check on the demands and depredations of the Romans. The imagination  of Petrarch might create the presence of a mighty people; and I hestitate to believe that even in the fourteenth century they could be reduced to a contemptible list of thirty-three thousand inhabitants. From that period to the reign of Leo the Tenth, if they multiplied to the amount of eighty-five thousand, the increase of citizens was in some degree pernicious to the ancient city.\nIV. I have reserved for the last the most potent and forcible cause of destruction, the domestic hostilities of the Romans themselves. Under the dominion of the Greek and French emperors, the peace of the city was disturbed by accidental though frequent seditions: it is from the decline of the latter, from the beginning of the tenth century, that we may date the licentiousness of private war, which violated with impunity the laws of the Code and the Gospel, without respecting the majesty of the absent sovereign or the presence and person of the vicar of Christ. In a dark period of five hundred years, Rome was perpetually afflicted by the sanguinary quarrels of the nobles and the people, the Guelphs and Ghibelines, the Colonna and Ursini; and, if much has escaped the knowledge, and much is unworthy of the notice, of history, I have exposed in the two preceding chapters the causes and effects of the public disorders. At such a time, when every quarrel was decided by the sword and none could trust their lives or properties to the impotence of law, the powerful citizens were armed for safety or offence against the domestic enemies whom they feared or hated. Except Venice alone, the same dangers and designs were common  to all the free republics of Italy; and the nobles usurped the prerogative of fortifying their houses, and erecting strong towers that were capable of resisting a sudden attack. The cities were filled with these hostile edifices; and the example of Lucca, which contained three hundred towers, her law, which confined their height to the measure of fourscore feet, may be extended, with suitable latitude, to the more opulent and populous states. The first step of the senator Brancaleone in the establishment of peace and justice was to demolish (as we have already seen) one hundred and forty of the towers of Rome; and in the last days of anarchy and discord, as late as the reign of Martin the Fifth, forty-four still stood in one of the thirteen or fourteen regions of the city. To this mischievous purpose, the remains of antiquity were most readily adapted: the temples and arches afforded a broad and solid basis for the new structures of brick and stone; and we can name the modern turrets that were raised on the triumphal monuments of Julius Caesar, Titus, and the Antonines. With some slight alterations, a theatre, an amphitheatre, a mausoleum, was transformed into a strong and spacious citadel. I need not repeat that the mole of Hadrian has assumed the title and form of the castle of St. Angelo; the Septizonium of Severus was  capable of standing against a royal army; the sepulchre of Metella has sunk under its outworks; the theatres of Pompey and Marcellus were occupied by the Savelli and Ursini families; and the rough fortress has been gradually softened to the splendour and elegance of an Italian palace. Even the churches were encompassed with arms and bulwarks, and the military engines on the roof of St. Peter's were the terror of the Vatican and the scandal of the Christian world. Whatever is fortified will be attacked; and whatever is attacked may be destroyed. Could the Romans have wrested from the popes the castle of St. Angelo, they had resolved, by a public decree, to annihilate that monument of servitude. Every building of defence was exposed to a  siege; and in every siege the arts and engines of destruction were laboriously employed. After the death of Nicholas the Fourth, Rome, without a sovereign or a senate, was abandoned six months to the fury of civil war. The houses, says a cardinal and poet of the times, were crushed by the weight and velocity of enormous stones; the walls were perforated by the strokes of the battering-ram; the towers were involved in fire and smoke; and the assailants were stimulated by rapine and revenge. The work was consummated by the tyranny of the laws; and the factions of Italy alternately exercised a blind and thoughtless vengeance on their adversaries, whose houses and castles they rased to the ground. In comparing the days of foreign, with the ages of domestic, hostility, we must pronounce that the latter have been far more ruinous to the city; and our opinion is confirmed by the evidence of Petrarch. Behold, says the laureat, the relics of Rome, the image of her pristine greatness! neither time nor the Barbarian can boast the merit of this stupendous destruction: it was perpetrated by her own citizens, by the most illustrious of her sons; and your ancestors (he writes to a noble Annibaldi) have done with the  battering-ram, what the Punic hero could not accomplish with the sword. The influence of the two last principles of decay must, in some degree, be multiplied by each other; since the houses and towers, which were subverted by civil war, required a new and perpetual supply from the monuments of antiquity.\nThese general observations may be separately applied to the amphitheatre of Titus, which has obtained the name of the Coliseum, either from its magnitude or from Nero's colossal statue: an edifice, had it been left to time and nature, which might, perhaps, have claimed an eternal duration. The curious antiquaries, who have computed the numbers and seats, are disposed to believe that, above the upper row of stone steps, the amphitheatre was encircled and elevated with several stages of wooden galleries, which were repeatedly consumed by fire and restored by the emperors. Whatever was precious, or portable, or profane, the statues of gods and heroes, and the costly ornaments of sculpture, which were cast in brass, or overspread with leaves of silver and gold, became the first prey of conquest or fanaticism, of the avarice of the Barbarians or the Christians. In the massy stones of    the Coliseum many holes are discerned; and the two most probable conjectures represent the various accidents of its decay. These stones were connected by solid links of brass or iron, nor had the eye of rapine overlooked the value of the baser metals: the vacant space was converted into a fair or market; the artisans of the Coliseum are mentioned in an ancient survey; and the chasms were perforated or enlarged, to receive the poles that supported the shops or tents of the mechanic trades. Reduced to its naked majesty, the Flavian amphitheatre was contemplated with awe and admiration by the pilgrims of the North; and their rude enthusiasm broke forth in a sublime proverbial expression, which is recorded in the eighth century, in the fragments of the venerable Bede: As long as the Coliseum stands, Rome shall stand; when the Coliseum falls, Rome will fall; when Rome falls, the world will fall. In the modern system of war, a situation commanded by three hills would not be chosen for a fortress; but the strength of the walls and arches could resist the engines of assault; a numerous garrison might be lodged in the enclosure; and, while one faction occupied the Vatican and the Capitol, the other was intrenched in the Lateran and the Coliseum.\n\n\n\n\nThe abolition at Rome of the ancient games must be understood with some latitude; and the carnival sports of the Testacean Mount and the Circus Agonalis were regulated by the law or custom of the city. The senator presided with dignity and pomp to adjudge and distribute the prizes, the gold ring, or the pallium, as it was styled, of cloth or silk. A tribute on the Jews supplied the annual expense; and the races, on foot, on horseback, or in chariots, were ennobled by a tilt and tournament of seventy-two of the Roman youth. In the year one thousand three hundred and thirty-two, a bull-feast, after the fashion of the Moors and Spaniards, was celebrated in the Coliseum itself; and the living manners are painted in a diary of the times. A convenient order of benches was restored; and a general proclamation, as far as Rimini and Ravenna, invited the nobles to exercise their skill and courage in this perilous adventure. The Roman ladies were  marshalled in three squadrons, and seated in three balconies, which on this day, the third of September, were lined with scarlet cloth. The fair Jacova di Rovere led the matrons from beyond the Tiber, a pure and native race, who still represent the features and character of antiquity. The remainder of the city was divided, as usual, between the Colonna and Ursini; the two factions were proud of the number and beauty of their female bands: the charms of Savella Ursini are mentioned with praise; and the Colonna regretted the absence of the youngest of their house, who had sprained her ankle in the garden of Nero's tower. The lots of the champions were drawn by an old and respectable citizen; and they descended into the arena, or pit, to encounter the wild bulls on foot, as it should seem, with a single spear. Amidst the crowd, our annalist has selected the names, colours, and devices of twenty of the most conspicuous knights. Several of the names are the most illustrious of Rome and the ecclesiastical state; Malatesta, Polenta, della Valle, Cafarello, Savelli, Capoccio, Conti, Annibaldi, Altieri, Corsi; the colours were adapted to their taste and situation; the devices are expressive of hope or despair, and breathe the spirit of gallantry and arms. I am alone like the youngest of the Horatii, the confidence of an intrepid stranger; I live disconsolate, a weeping widower; I burn under the ashes, a discreet lover; I adore Lavinia, or Lucretia, the ambiguous declaration of a modern passion; My faith is as pure, the motto of a white livery; Who is stronger than myself? of a lion's hide; If I am drowned in blood, what a pleasant death! the wish of ferocious courage. The pride or prudence of the Ursini restrained them from the field, which was occupied by three of their hereditary rivals, whose inscriptions denoted the lofty greatness of the Colonna name: Though sad, I am strong; Strong as I am great; If I fall, addressing himself to the spectators, you fall with me; — intimating (says the contemporary writer) that, while the other families were the subjects of the Vatican, they  alone were the supporters of the Capitol. The combats of the amphitheatre were dangerous and bloody. Every champion successively encountered a wild bull; and the victory may be ascribed to the quadrupeds, since no more than eleven were left on the field, with the loss of nine wounded, and eighteen killed, on the side of their adversaries. Some of the noblest families might mourn, but the pomp of the funerals, in the churches of St. John Lateran and Sta. Maria Maggiore, afforded a second holiday to the people. Doubtless it was not in such conflicts that the blood of the Romans should have been shed; yet, in blaming their rashness, we are compelled to applaud their gallantry; and the noble volunteers, who display their magnificence and risk their lives under the balconies of the fair, excite a more generous sympathy than the thousands of captives and malefactors who were reluctantly dragged to the scene of slaughter.\nThis use of the amphitheatre was a rare, perhaps a singular, festival: the demand for the materials was a daily and continual want, which the citizens could gratify without restraint or remorse. In the fourteenth century, a scandalous act of concord secured to both factions the privilege of extracting stones from the free and common quarry of the Coliseum; and Poggius laments that the greater part of these stones had been burnt to lime by the folly of the Romans. To check this abuse, and to prevent the nocturnal  crimes that might be perpetrated in the vast and gloomy recess, Eugenius the Fourth surrounded it with a wall; and, by a charter long extant, granted both the ground and edifice to the monks of an adjacent convent. After his death, the wall was overthrown in a tumult of the people; and, had they themselves respected the noblest monument of their fathers, they might have justified the resolve that it should never be degraded to private property. The inside was damaged; but, in the middle of the sixteenth century, an era of taste and learning, the exterior circumference of one thousand six hundred and twelve feet was still entire and inviolate; a triple elevation of fourscore arches, which rose to the height of one hundred and eight feet. Of the present ruin the nephews of Paul the Third are the guilty agents; and every traveller who views the Farnese palace may curse the sacrilege and luxury of these upstart princes. A similar reproach is applied to the Barberini; and the repetition of injury might be dreaded from every reign, till the Coliseum was placed under the safeguard of religion by the most liberal of the pontiffs, Benedict the Fourteenth, who consecrated a spot which persecution and fable had stained with the blood of so many Christian martyrs.\n\nWhen Petrarch first gratified his eyes with a view of those monuments whose scattered fragments so far surpass the most eloquent descriptions, he was astonished at the supine indifference of the Romans themselves; he was humbled rather than elated by the discovery that, except his friend Rienzi and one of the Colonna, a stranger of the Rhône was more conversant with these antiquities than the nobles and natives of the metropolis. The ignorance and credulity of the Romans are elaborately displayed in the old survey of the city, which was composed about the beginning of the thirteenth century; and, without dwelling on the manifold errors of name and place, the legend of the Capitol may provoke a smile of contempt and indignation. The Capitol, says the anonymous writer, is so named as being the head of the world; where the consuls and senators formerly resided for the government of the city and the globe. The strong and lofty walls were covered with glass and gold, and crowned with a roof of the richest and most curious carving.  Below the citadel stood a palace, of gold for the greatest part, decorated with precious stones, and whose value might be estimated at one third of the world itself. The statues of all the provinces were arranged in order, each with a small bell suspended from its neck; and such was the contrivance of art-magic that, if the province rebelled against Rome, the statue turned round to that quarter of the heavens, the bell rang, the prophet of the Capitol reported the prodigy, and the senate was admonished of the impending danger. A second example of less importance, though of equal absurdity, may be drawn from the two marble horses, led by two naked youths, which have since been transported from the baths of Constantine to the Quirinal Hill. The groundless application of the names of Phidias and Praxiteles may perhaps be excused; but these Grecian sculptors should not have been removed above four hundred years from the age of Pericles to that of Tiberius; they should not have been transformed into two philosophers or magicians, whose nakedness was the symbol of truth and knowledge, who revealed to the emperor his most secret actions, and, after refusing all pecuniary recompense, solicited the honour of leaving this eternal monument of themselves. Thus awake to the power of magic, the Romans were insensible to the beauties of art: no more than five statues were visible to the eyes of Poggius; and, of the multitudes which chance or design had buried under the ruins, the resurrection was fortunately delayed till a safcr and more enlightened age. The Nile, which now adorns  the Vatican, had been explored by some labourers in digging a vineyard near the temple, or convent, of the Minerva; but the impatient proprietor, who was tormented by some visits of curiosity, restored the unprofitable marble to its former grave. The discovery of a statue of Pompey, ten feet in length, was the occasion of a law-suit. It had been found under a partition-wall: the equitable judge had pronounced that the head should be separated from the body, to satisfy the claims of the contiguous owners; and the sentence would have been executed, if the intercession of a cardinal and the liberality of a pope had not rescued the Roman hero from the hands of his Barbarous countrymen.\nBut the clouds of Barbarism were gradually dispelled; and the peaceful authority of Martin the Fifth and his successors restored the ornaments of the city as well as the order of the ecclesiastical state. The improvements of Rome, since the fifteenth century, have not been the spontaneous produce of freedom and industry. The first and most natural root of a great city is the labour and populousness of the adjacent country, which supplies the materials of subsistence, of manufactures, and of foreign trade. But the greater part of the Campagna of Rome is reduced to a dreary and desolate wilderness; the overgrown estates of the princes and the clergy are cultivated by the lazy hands of indigent and hopeless vassals; and the scanty harvests are confined or exported  for the benefit of a monopoly. A second and more artificial cause of the growth of a metropolis is the residence of a monarch, the expense of a luxurious court, and the tributes of dependent provinces. Those provinces and tributes had been lost in the fall of the empire; and, if some streams of the silver of Peru and the gold of Brazil have been attracted by the Vatican, the revenues of the cardinals, the fees of office, the oblations of pilgrims and clients, and the remnant of ecclesiastical taxes afford a poor and precarious supply, which maintains, however, the idleness of the court and city. The population of Rome, far below the measure of the great capitals of Europe, does not exceed one hundred and seventy thousand inhabitants; and, within the spacious enclosure of the walls, the largest portion of the seven hills is overspread with vineyards and ruins. The beauty and splendour of the modern city may be ascribed to the abuses of the government, to the influence of superstition. Each reign (the exceptions are rare) has been marked by the rapid elevation of a new family, enriched by the childless pontiff at the expense of the church and country. The palaces of these fortunate nephews are the most costly monuments of elegance and servitude; the perfect arts of architecture, painting, and sculpture have been prostituted in their service; and their galleries and gardens are decorated with the most precious works of antiquity, which taste or vanity has prompted them to collect. The ecclesiastical revenues were more decently employed by the popes themselves in the pomp of the Catholic worship; but it is superfluous to enumerate their pious foundations of altars, chapels, and churches, since these lesser stars are eclipsed by the sun of the Vatican, by the dome of St. Peter, the most glorious structure that ever has  been applied to the use of religion. The fame of Julius the Second, Leo the Tenth, and Sixtus the Fifth is accompanied by the superior merit of Bramante and Fontana, of Raphael and Michael-Angelo; and the same munificence which had been displayed in palaces and temples was directed with equal zeal to revive and emulate the labours of antiquity. Prostrate obelisks were raised from the ground and erected in the most conspicuous places; of the eleven aqueducts of the Caesars and Consuls, three were restored; the artificial rivers were conducted over a long series of old or of new arches, to discharge into marble basons a flood of salubrious and refreshing waters; and the spectator, impatient to ascend the steps of St. Peter's, is detained by a column of Egyptian granite, which rises between two lofty and perpetual fountains to the height of one hundred and twenty fect. The map, the description, the monuments of ancient Rome have been elucidated by the diligence of the antiquarian and the student; and the footsteps of heroes, the relics, not of superstition, but of empire, are devoutly visited by a new  race of pilgrims from the remote, and once savage, countries of the North.\nOf these pilgrims, and of every reader, the attention will be excited by an History of the Decline and Fall of the Roman Empire: the greatest, perhaps, and most awful scene in the history of mankind. The various causes and progressive effects are connected with many of the events most interesting in human annals: the artful policy of the Caesars, who long maintained the name and image of a free republic; the disorder of military despotism; the rise, establishment, and sects of Christianity; the foundation of Constantinople; the division of the monarchy; the invasion and settlements of the Barbarians of Germany and Scythia; the institutions of the civil law; the character and religion of Mahomet; the temporal sovereignty of the popes; the restoration and decay of the Western empire of Charlemagne; the crusades of the Latins in the East; the conquests of the Saracens and Turks; the ruin of the Greek empire; the state and revolutions of Rome in the middle age. The historian may applaud the importance and variety of his subject; but, while he is conscious of his own imperfections, he must often accuse the deficiency of his materials. It was among the ruins of the Capitol that I first conceived the idea of a work which has amused and exercised near twenty years of my life, and which, however inadequate to my own wishes, I finally deliver to the curiosity and candour of the public.\nLausanne, June 27, 1787.\n"




```python
first_chapter = gibbon_by_chapter['StringText'][0]
```

## Using `spaCy`'s off-the-shelf NER model

This model was trained on a wide variety of sources, so we can't expect it to be completely accurate. We'll revisit that problem soon. We can train our own NER model and it would do better, but this will take some time to do and requires a lot of set up. If you're interested in training your own model, reach out to me and we can work together on it.


```python
# pass the first chapter into spaCy parser
first_chapter_doc = nlp(first_chapter)
```


```python
for entity in first_chapter_doc.ents: # can access NER with the .ents attribute
    print(entity.text, entity.label_, sep='\t')
```

    the second century	DATE
    Christian	NORP
    Rome	GPE
    Roman	NORP
    more than fourscore years	DATE
    Nerva	GPE
    Trajan	GPE
    Hadrian	NORP
    two	CARDINAL
    Antonines	NORP
    two	CARDINAL
    Marcus Antoninus	GPE
    Romans	NORP
    senate	ORG
    seven first centuries	DATE
    Augustus	ORG
    Rome	GPE
    every day	DATE
    Augustus	ORG
    Rome	GPE
    Parthians	NORP
    Crassus	PRODUCT
    Aethiopia	GPE
    Arabia Felix	PERSON
    a thousand miles	QUANTITY
    Europe	LOC
    Germany	GPE
    first	ORDINAL
    Roman	NORP
    Augustus	PERSON
    senate	ORG
    the Atlantic Ocean	LOC
    Rhine	PERSON
    Danube	PERSON
    Euphrates	LOC
    Arabia	GPE
    Africa	LOC
    Augustus	ORG
    first	ORDINAL
    Caesars	ORG
    Imperial	ORG
    Roman	NORP
    Roman	NORP
    the first century	DATE
    Christian	NORP
    Britain	GPE
    Caesar	ORG
    Augustus	ORG
    Gaul	PERSON
    Britain	GPE
    continental	ORG
    about forty years	DATE
    Roman	NORP
    Britons	PERSON
    Caractacus	ORG
    Boadicea	GPE
    Druids	ORG
    Imperial	ORG
    Domitian	PERSON
    Agricola	ORG
    Caledonians	NORP
    Grampian	NORP
    Roman	NORP
    Britain	GPE
    Agricola	ORG
    Ireland	GPE
    one	CARDINAL
    Britons	PERSON
    Agricola	ORG
    Britain	GPE
    two	CARDINAL
    Scotland	GPE
    about forty miles	QUANTITY
    Antoninus Pius	GPE
    Antoninus	GPE
    Edinburgh	GPE
    Glasgow	PERSON
    Roman	NORP
    Caledonians	NORP
    gloomy hills	GPE
    winter	DATE
    Roman	NORP
    Imperial	ORG
    Augustus	ORG
    Trajan	ORG
    first	ORDINAL
    Trajan	PERSON
    Dacians	NORP
    Danube	PERSON
    Domitian	NORP
    Rome	GPE
    Dacian	NORP
    Trajan	GPE
    five years	DATE
    Dacia	PERSON
    second	ORDINAL
    Augustus	ORG
    about thirteen hundred miles	QUANTITY
    Dniester	ORG
    Theiss	NORP
    Tibiscus	ORG
    the Lower Danube	ORG
    the Euxine Sea	LOC
    Danube	PERSON
    Bender	PERSON
    Turkish	NORP
    Russian	NORP
    Trajan	PERSON
    Alexander	ORG
    Trajan	GPE
    Roman	NORP
    Philip	GPE
    Trajan	ORG
    Parthians	NORP
    Tigris	GPE
    Armenia	GPE
    the Persian gulf	LOC
    first	ORDINAL
    Roman	NORP
    Arabia	GPE
    Trajan	PERSON
    India	GPE
    senate	ORG
    Bosphorus	GPE
    Colchos	GPE
    Iberia	GPE
    Albania	GPE
    Osrhoene	PERSON
    Parthian	NORP
    Median	NORP
    Carduchian	NORP
    Armenia	GPE
    Mesopotamia	GPE
    Assyria	GPE
    Trajan	PERSON
    Capitol	FAC
    one	CARDINAL
    Roman	NORP
    Jupiter	LOC
    Roman	NORP
    many ages	DATE
    Terminus	ORG
    Jupiter	LOC
    Hadrian	NORP
    Trajan	PERSON
    first	ORDINAL
    Parthians	NORP
    Roman	NORP
    Armenia	GPE
    Mesopotamia	GPE
    Assyria	GPE
    Augustus	ORG
    Euphrates	LOC
    Hadrian	NORP
    Trajan	GPE
    Trajan	PERSON
    Hadrian	NORP
    Antoninus	GPE
    Caledonia	GPE
    the Upper Egypt	GPE
    Antoninus Pius	ORG
    Italy	GPE
    the twenty-three years	DATE
    Rome	GPE
    Lanuvian	NORP
    Augustus	ORG
    Hadrian	NORP
    two	CARDINAL
    Antonines	NORP
    Roman	NORP
    forty-three years	DATE
    Hadrian	NORP
    Antoninus Pius	ORG
    Roman	NORP
    Roman	NORP
    Hadrian	NORP
    Antoninus	LOC
    Parthians	NORP
    Germans	NORP
    Marcus	PERSON
    Marcus	GPE
    Euphrates	LOC
    Danube	ORG
    Roman	NORP
    Roman	NORP
    Roman	NORP
    Europe	LOC
    first	ORDINAL
    Roman	NORP
    the hour	TIME
    Roman	NORP
    Imperial	ORG
    Romans	NORP
    the evening	TIME
    daily	DATE
    winter-quarters	DATE
    Roman	NORP
    Pyrrhic	PRODUCT
    Roman	NORP
    Hadrian	NORP
    Trajan	PERSON
    Roman	NORP
    Nine centuries	DATE
    Polybius	ORG
    Punic	ORG
    Caesar	ORG
    Hadrian	NORP
    Antonines	PRODUCT
    Imperial	ORG
    ten	CARDINAL
    fifty-five	CARDINAL
    first	ORDINAL
    eleven hundred and five	CARDINAL
    nine	CARDINAL
    five hundred and fifty-five	CARDINAL
    six thousand one hundred	CARDINAL
    four feet	QUANTITY
    two and a half	DATE
    about six feet	QUANTITY
    eighteen inches	QUANTITY
    only ten or twelve	CARDINAL
    Roman	NORP
    Spanish	LANGUAGE
    eight	CARDINAL
    three feet	QUANTITY
    Greeks	PERSON
    Macedonians	NORP
    sixteen	CARDINAL
    ten	CARDINAL
    first	ORDINAL
    first	ORDINAL
    an hundred and thirty-two	CARDINAL
    nine	CARDINAL
    sixty-six	CARDINAL
    seven hundred and twenty-six	CARDINAL
    Rome	GPE
    Italy	GPE
    Trajan	PERSON
    Hadrian	NORP
    Spain	GPE
    Cappadocia	PERSON
    Roman	NORP
    East	LOC
    oblong	GPE
    Rome	GPE
    Romans	NORP
    Roman	NORP
    ten	CARDINAL
    fifty-five	CARDINAL
    Roman	NORP
    about seven hundred yards	QUANTITY
    twenty thousand	CARDINAL
    Romans	NORP
    two hundred feet	QUANTITY
    twelve feet	QUANTITY
    twelve feet	QUANTITY
    many days	DATE
    about six hours	TIME
    twenty miles	QUANTITY
    first	ORDINAL
    Roman	NORP
    six thousand eight hundred and thirty-one	DATE
    Romans	NORP
    about twelve thousand five hundred	CARDINAL
    Hadrian	NORP
    three hundred and seventy-five thousand	CARDINAL
    Romans	NORP
    Three	CARDINAL
    Britain	GPE
    Rhine	PERSON
    Danube	PERSON
    sixteen	CARDINAL
    two	CARDINAL
    Lower	LOC
    three	CARDINAL
    the Upper Germany	GPE
    one	CARDINAL
    Rhaetia	ORG
    Noricum	GPE
    four	CARDINAL
    Pannonia	GPE
    three	CARDINAL
    Maesia	GPE
    two	CARDINAL
    Dacia	PERSON
    Euphrates	ORG
    eight	CARDINAL
    six	CARDINAL
    Syria	GPE
    two	CARDINAL
    Cappadocia	PERSON
    Egypt	GPE
    Africa	LOC
    Spain	GPE
    Italy	GPE
    twenty thousand	CARDINAL
    City Cohorts and	FAC
    Praetorians	NORP
    navy	ORG
    Romans	NORP
    Tyre	ORG
    Marseilles	GPE
    Romans	NORP
    Mediterranean	LOC
    Augustus	ORG
    two	CARDINAL
    Italy	GPE
    Ravenna	PERSON
    Adriatic	NORP
    Misenum	GPE
    Naples	GPE
    two	CARDINAL
    three	CARDINAL
    Augustus	ORG
    Actium	ORG
    Liburnians	LOC
    Liburnians	NORP
    two	CARDINAL
    Ravenna	PERSON
    Misenum	GPE
    Mediterranean	LOC
    several thousand	CARDINAL
    two	CARDINAL
    Roman	NORP
    Frejus	DATE
    Provence	GPE
    Euxine	PERSON
    forty	CARDINAL
    three thousand	CARDINAL
    Gaul	PERSON
    Britain	GPE
    Rhine	PERSON
    Danube	PERSON
    Imperial	ORG
    more than four hundred and fifty thousand	CARDINAL
    the last century	DATE
    Roman	NORP
    Hadrian	NORP
    Antonines	ORG
    Spain	GPE
    Europe	LOC
    Mediterranean	LOC
    the Atlantic Ocean	LOC
    two	CARDINAL
    Augustus	ORG
    three	CARDINAL
    Lusitania	GPE
    Baetica	GPE
    Tarraconensis	ORG
    Portugal	GPE
    Lusitanians	NORP
    East	LOC
    North	LOC
    Grenada	GPE
    Andalusia	GPE
    Baetica	GPE
    Spain	GPE
    Asturias	GPE
    Biscay	GPE
    Navarre	PERSON
    Leon	PERSON
    two	CARDINAL
    Castilles	GPE
    Murcia	GPE
    Valencia	GPE
    Catalonia	GPE
    Arragon	GPE
    third	ORDINAL
    Roman	NORP
    Tarragona	GPE
    Celtiberians	NORP
    Cantabrians	NORP
    Asturians	NORP
    Rome	GPE
    first	ORDINAL
    Arabs	NORP
    Ancient Gaul	ORG
    Alps	GPE
    Rhine	GPE
    Ocean	LOC
    France	GPE
    Alsace	GPE
    Lorraine	PERSON
    Savoy	ORG
    Switzerland	GPE
    four	CARDINAL
    Rhine	GPE
    Liege	GPE
    Luxemburg	GPE
    Hainault	ORG
    Flanders	ORG
    Brabant	PERSON
    Augustus	PERSON
    Gaul	ORG
    hundred	CARDINAL
    Mediterranean	LOC
    Languedoc	GPE
    Provence	ORG
    Dauphiné	ORG
    Narbonne	PERSON
    Aquitaine	ORG
    Seine	PRODUCT
    the Celtic Gaul	LOC
    Lugdunum	GPE
    Lyons	WORK_OF_ART
    Belgic	PERSON
    Seine	ORG
    Rhine	GPE
    Caesar	ORG
    Germans	NORP
    Belgic	PERSON
    Roman	NORP
    Gallic	PERSON
    Rhine	GPE
    Basil to Leyden	PERSON
    the Lower Germany	GPE
    Antonines	NORP
    six	CARDINAL
    Gaul	ORG
    Narbonnese	NORP
    Aquitaine	ORG
    Celtic	ORG
    Lyonnese	NORP
    Belgic	ORG
    two	CARDINAL
    Britain	GPE
    Roman	NORP
    England	GPE
    Wales	GPE
    Scotland	GPE
    the Friths of Dumbarton	ORG
    Edinburgh	GPE
    Britain	GPE
    Belgae	ORG
    Brigantes	GPE
    North	LOC
    South Wales	GPE
    Iceni	NORP
    Norfolk	GPE
    Suffolk	GPE
    Spain	GPE
    Gaul	ORG
    Britain	GPE
    Roman	NORP
    European	NORP
    Hercules	GPE
    Antoninus	GPE
    Tagus	ORG
    Rhine	PERSON
    Danube	PERSON
    Roman	NORP
    Lombardy	LOC
    Italy	GPE
    Gauls	PERSON
    Po	LOC
    Piedmont	GPE
    Romagna	GPE
    Alps	LOC
    Ligurians	NORP
    Genoa	ORG
    Venice	GPE
    Adige	ORG
    Venetians	NORP
    Tuscany	PERSON
    Etruscans	NORP
    Umbrians	PERSON
    Italy	GPE
    first	ORDINAL
    Tiber	PERSON
    seven	CARDINAL
    Rome	GPE
    Sabines	ORG
    Latins	ORG
    Volsci	PRODUCT
    Naples	GPE
    first	ORDINAL
    Campania	GPE
    Naples	GPE
    Marsi	ORG
    Samnites	ORG
    Apulians	NORP
    Lucanians	NORP
    Augustus	PERSON
    Italy	GPE
    eleven	CARDINAL
    Istria	GPE
    Roman	NORP
    European	NORP
    Rome	GPE
    Rhine	PERSON
    Danube	PERSON
    only thirty miles	QUANTITY
    thirteen hundred miles	QUANTITY
    sixty	CARDINAL
    six	CARDINAL
    Euxine	PERSON
    Danube	PERSON
    Illyricum	GPE
    Illyrian	NORP
    Rhaetia, Noricum	ORG
    Pannonia	GPE
    Dalmatia	ORG
    Dacia	PERSON
    Maesia	GPE
    Thrace	GPE
    Macedonia	GPE
    Greece	GPE
    Rhaetia	ORG
    Vindelicians	NORP
    Alps	LOC
    Danube	ORG
    Inn	ORG
    Bavaria	GPE
    Augsburg	GPE
    German	NORP
    Grisons	PERSON
    Tyrol	ORG
    Austria	GPE
    Inn	ORG
    Danube	ORG
    Save	ORG
    Austria	GPE
    Styria	GPE
    Carinthia	GPE
    Carniola	ORG
    the Lower Hungary	GPE
    Sclavonia	GPE
    Noricum	GPE
    Pannonia	GPE
    Roman	NORP
    German	NORP
    Romans	NORP
    Austrian	NORP
    Bohemia	PERSON
    Moravia	GPE
    Austria	GPE
    Hungary	GPE
    Theiss	NORP
    Danube	PERSON
    Austria	GPE
    Roman	NORP
    Dalmatia	ORG
    Illyricum	GPE
    Save	ORG
    Adriatic	NORP
    Venetian	NORP
    Sclavonian	NORP
    Croatia	GPE
    Bosnia	GPE
    Austrian	NORP
    Turkish	NORP
    Christian	NORP
    Mahometan	NORP
    Danube	PERSON
    Theiss	NORP
    Save	ORG
    Greeks	WORK_OF_ART
    Ister	ORG
    Maesia	GPE
    Dacia	PERSON
    Trajan	ORG
    Danube	PERSON
    Temeswar	ORG
    Transylvania	GPE
    Hungary	GPE
    Moldavia	GPE
    Walachia	GPE
    the Ottoman Porte	GPE
    Danube	ORG
    Maesia	GPE
    the middle ages	DATE
    Servia	GPE
    Bulgaria	GPE
    Turkish	NORP
    Roumelia	PERSON
    Turks	NORP
    Thrace	GPE
    Macedonia	GPE
    Greece	GPE
    Roman	NORP
    Antonines	ORG
    Thrace	ORG
    Haemus	PERSON
    Rhodope	PERSON
    Bosphorus	PERSON
    Hellespont	GPE
    Rome	GPE
    Constantine	ORG
    Bosphorus	PERSON
    Macedonia	GPE
    Alexander	ORG
    Asia	LOC
    two	CARDINAL
    Philips	ORG
    Epirus	ORG
    Thessaly	GPE
    Aegean	NORP
    Ionian	NORP
    Thebes	GPE
    Argos	LOC
    Sparta	GPE
    Athens	GPE
    Greece	GPE
    Roman	NORP
    Achaean	NORP
    Achaia	GPE
    Europe	LOC
    Roman	NORP
    Asia	LOC
    Trajan	GPE
    Turkish	NORP
    Asia Minor	ORG
    Euxine	PERSON
    Mediterranean	LOC
    Euphrates	LOC
    Europe	LOC
    Mount Taurus	FAC
    Halys	PERSON
    Romans	NORP
    Asia	LOC
    Troy	GPE
    Lydia	ORG
    Phrygia	ORG
    Pamphylians	NORP
    Lycians	NORP
    Carians	NORP
    Grecian	NORP
    Ionia	GPE
    Bithynia	GPE
    Pontus	ORG
    Constantinople	GPE
    Trebizond	GPE
    Cilicia	PERSON
    Syria	GPE
    the Roman Asia	LOC
    Halys	PERSON
    Armenia	GPE
    Euphrates	ORG
    Cappadocia	PERSON
    Euxine	PERSON
    Trebizond	GPE
    Asia	LOC
    Danube	PERSON
    Europe	LOC
    Roman	NORP
    Crim Tartary	PERSON
    Circassia	GPE
    Mingrelia	GPE
    Alexander	GPE
    Syria	GPE
    Seleucidae	ORG
    Upper Asia	LOC
    Parthians	NORP
    Euphrates	LOC
    Mediterranean	LOC
    Syria	GPE
    Romans	NORP
    Cappadocia	PERSON
    Egypt	GPE
    the Red Sea	LOC
    Phoenicia	ORG
    Palestine	GPE
    Syria	GPE
    Wales	GPE
    Phoenicia	ORG
    Palestine	GPE
    America	GPE
    Europe	LOC
    Syria	GPE
    Euphrates	LOC
    the Red Sea	LOC
    Arabs	NORP
    Roman	NORP
    Egypt	GPE
    Africa	LOC
    Asia	LOC
    Egypt	GPE
    Roman	NORP
    Ptolemies	ORG
    Mamelukes	ORG
    Turkish	NORP
    Nile	LOC
    five hundred miles	QUANTITY
    Cancer	PERSON
    Mediterranean	LOC
    Cyrene	PERSON
    first	ORDINAL
    Greek	NORP
    Egypt	GPE
    Barca	GPE
    Cyrene	PERSON
    Africa	LOC
    above fifteen hundred miles	QUANTITY
    Mediterranean	LOC
    Sahara	LOC
    an hundred miles	QUANTITY
    Romans	NORP
    Africa	LOC
    Phoenician	NORP
    Libyans	NORP
    Tripoli	GPE
    Algiers	LOC
    Numidia	GPE
    Massinissa	PERSON
    Jugurtha	GPE
    Augustus	ORG
    Numidia	GPE
    at least two-thirds	CARDINAL
    Mauritania	GPE
    Caesariensis	PRODUCT
    Mauritania	GPE
    Moors	ORG
    Tingi	ORG
    Tangier	ORG
    Tingitana	GPE
    Sallè	NORP
    Ocean	LOC
    Romans	NORP
    Mequinez	ORG
    Morocco	GPE
    Segelmessa	PERSON
    Roman	NORP
    Africa	LOC
    Mount Atlas	ORG
    Roman	NORP
    Africa	LOC
    Spain	GPE
    about twelve miles	QUANTITY
    Atlantic	LOC
    Mediterranean	LOC
    Hercules	GPE
    two	CARDINAL
    European	NORP
    Gibraltar	LOC
    the Mediterranean Sea	LOC
    Roman	NORP
    two	CARDINAL
    Baleares	PRODUCT
    Majorca and Minorca	ORG
    Spain	GPE
    Great Britain	GPE
    Corsica	PRODUCT
    Two	CARDINAL
    Italian	NORP
    Sardinia	GPE
    Sicily	GPE
    Crete	ORG
    Candia	PERSON
    Cyprus	PRODUCT
    Greece	GPE
    Asia	LOC
    Turkish	NORP
    Malta	GPE
    Order	ORG
    Roman	NORP
    Rome	GPE
    two thousand miles	QUANTITY
    Antoninus	GPE
    Dacia	PERSON
    Mount Atlas	ORG
    Cancer	WORK_OF_ART
    more than	CARDINAL
    the Western Ocean	LOC
    Euphrates	ORG
    fifty-sixth	CARDINAL
    hundred thousand square miles	QUANTITY



```python
# filter by place name
for entity in first_chapter_doc.ents:
    if (entity.label_ == 'GPE') or (entity.label_ == 'LOC'):
        print(entity.text)
```

    Rome
    Nerva
    Trajan
    Marcus Antoninus
    Rome
    Rome
    Aethiopia
    Europe
    Germany
    the Atlantic Ocean
    Euphrates
    Arabia
    Africa
    Britain
    Britain
    Boadicea
    Britain
    Ireland
    Britain
    Scotland
    Antoninus Pius
    Antoninus
    Edinburgh
    gloomy hills
    Rome
    Trajan
    the Euxine Sea
    Trajan
    Philip
    Tigris
    Armenia
    the Persian gulf
    Arabia
    India
    Bosphorus
    Colchos
    Iberia
    Albania
    Armenia
    Mesopotamia
    Assyria
    Jupiter
    Jupiter
    Armenia
    Mesopotamia
    Assyria
    Euphrates
    Trajan
    Antoninus
    Caledonia
    the Upper Egypt
    Italy
    Rome
    Antoninus
    Marcus
    Euphrates
    Europe
    Rome
    Italy
    Spain
    East
    oblong
    Rome
    Britain
    Lower
    the Upper Germany
    Noricum
    Pannonia
    Maesia
    Syria
    Egypt
    Africa
    Spain
    Italy
    Marseilles
    Mediterranean
    Italy
    Misenum
    Naples
    Liburnians
    Misenum
    Mediterranean
    Provence
    Britain
    Spain
    Europe
    Mediterranean
    the Atlantic Ocean
    Lusitania
    Baetica
    Portugal
    East
    North
    Grenada
    Andalusia
    Baetica
    Spain
    Asturias
    Biscay
    Castilles
    Murcia
    Valencia
    Catalonia
    Arragon
    Tarragona
    Rome
    Alps
    Rhine
    Ocean
    France
    Alsace
    Switzerland
    Rhine
    Liege
    Luxemburg
    Mediterranean
    Languedoc
    the Celtic Gaul
    Lugdunum
    Rhine
    Rhine
    the Lower Germany
    Britain
    England
    Wales
    Scotland
    Edinburgh
    Britain
    Brigantes
    North
    South Wales
    Norfolk
    Suffolk
    Spain
    Britain
    Hercules
    Antoninus
    Lombardy
    Italy
    Po
    Piedmont
    Romagna
    Alps
    Venice
    Italy
    Rome
    Naples
    Campania
    Naples
    Italy
    Istria
    Rome
    Illyricum
    Pannonia
    Maesia
    Thrace
    Macedonia
    Greece
    Alps
    Bavaria
    Augsburg
    Austria
    Austria
    Styria
    Carinthia
    the Lower Hungary
    Sclavonia
    Noricum
    Pannonia
    Moravia
    Austria
    Hungary
    Austria
    Illyricum
    Croatia
    Bosnia
    Maesia
    Transylvania
    Hungary
    Moldavia
    Walachia
    the Ottoman Porte
    Maesia
    Servia
    Bulgaria
    Thrace
    Macedonia
    Greece
    Hellespont
    Rome
    Macedonia
    Asia
    Thessaly
    Thebes
    Argos
    Sparta
    Athens
    Greece
    Achaia
    Europe
    Asia
    Trajan
    Mediterranean
    Euphrates
    Europe
    Asia
    Troy
    Ionia
    Bithynia
    Constantinople
    Trebizond
    Syria
    the Roman Asia
    Armenia
    Trebizond
    Asia
    Europe
    Circassia
    Mingrelia
    Alexander
    Syria
    Upper Asia
    Euphrates
    Mediterranean
    Syria
    Egypt
    the Red Sea
    Palestine
    Syria
    Wales
    Palestine
    America
    Europe
    Syria
    Euphrates
    the Red Sea
    Egypt
    Africa
    Asia
    Egypt
    Nile
    Mediterranean
    Egypt
    Barca
    Africa
    Mediterranean
    Sahara
    Africa
    Tripoli
    Algiers
    Numidia
    Jugurtha
    Numidia
    Mauritania
    Mauritania
    Tingitana
    Ocean
    Morocco
    Africa
    Africa
    Spain
    Atlantic
    Mediterranean
    Hercules
    Gibraltar
    the Mediterranean Sea
    Spain
    Great Britain
    Sardinia
    Sicily
    Greece
    Asia
    Malta
    Rome
    Antoninus
    the Western Ocean



```python
# putting all of the data into a dictionary
import collections
place_freq = collections.defaultdict(int)
for entity in first_chapter_doc.ents:
    if (entity.label_ == 'GPE') or (entity.label_ == 'LOC'):
        place_freq[entity.text] += 1 # the utility of defaultdict!
place_freq = dict(place_freq)
```


```python
# dict -> df
place_freq_df = pd.DataFrame.from_dict(place_freq, orient='index').reset_index().rename(columns={'index':'place_name',0:'frequency'})
place_freq_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place_name</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nerva</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Trajan</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Marcus Antoninus</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aethiopia</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>147</th>
      <td>Great Britain</td>
      <td>1</td>
    </tr>
    <tr>
      <th>148</th>
      <td>Sardinia</td>
      <td>1</td>
    </tr>
    <tr>
      <th>149</th>
      <td>Sicily</td>
      <td>1</td>
    </tr>
    <tr>
      <th>150</th>
      <td>Malta</td>
      <td>1</td>
    </tr>
    <tr>
      <th>151</th>
      <td>the Western Ocean</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>152 rows × 2 columns</p>
</div>




```python
# downloading the Pleiades data we need (also from my gh)
if not os.path.isfile('places.csv'):
    wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/places.csv')
if not os.path.isfile('names.csv'):
    wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/names.csv')
```


```python
places = pd.read_csv('places.csv')
places
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>created</th>
      <th>description</th>
      <th>details</th>
      <th>provenance</th>
      <th>title</th>
      <th>uri</th>
      <th>id</th>
      <th>representative_latitude</th>
      <th>representative_longitude</th>
      <th>bounding_box_wkt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2010-06-24T14:11:06Z</td>
      <td>The ancient region known to the Romans as "Gal...</td>
      <td>&lt;p&gt;The Barrington Atlas Directory notes: FRA&lt;/p&gt;</td>
      <td>Barrington Atlas: BAtlas 1 D1 Gallia</td>
      <td>Gallia</td>
      <td>https://pleiades.stoa.org/places/993</td>
      <td>993</td>
      <td>46.705437</td>
      <td>1.013706</td>
      <td>POLYGON ((8.672222 42.4395125, 8.672222 51.981...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-01-10T20:52:00Z</td>
      <td>A Roman house in Pompeii (I, 6, 15), also know...</td>
      <td>&lt;p&gt;The house was excavated in 1913 and 1914. T...</td>
      <td>Pleiades</td>
      <td>House of the Ceii</td>
      <td>https://pleiades.stoa.org/places/999909607</td>
      <td>999909607</td>
      <td>40.750010</td>
      <td>14.489506</td>
      <td>POLYGON ((14.4895058 40.7500099, 14.4895058 40...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2015-05-28T11:48:45Z</td>
      <td>The so-called "House of the Lararium of Achill...</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>House of the Lararium of Achilles</td>
      <td>https://pleiades.stoa.org/places/999909608</td>
      <td>999909608</td>
      <td>40.750362</td>
      <td>14.489286</td>
      <td>POLYGON ((14.489286 40.750362, 14.489286 40.75...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-03-20T15:26:53Z</td>
      <td>A necropolis with inhumations dating to the fi...</td>
      <td>&lt;p&gt;A necropolis located close to Monte Bibele ...</td>
      <td>Pleiades</td>
      <td>Monte Tamburino necropolis</td>
      <td>https://pleiades.stoa.org/places/999917206</td>
      <td>999917206</td>
      <td>44.272322</td>
      <td>11.375880</td>
      <td>POLYGON ((11.3758803 44.2723222, 11.3758803 44...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2018-05-30T03:08:22Z</td>
      <td>The megalithic defensive circuit of Rusellae d...</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Circuit wall of Rusellae</td>
      <td>https://pleiades.stoa.org/places/999951524</td>
      <td>999951524</td>
      <td>42.829089</td>
      <td>11.163588</td>
      <td>POLYGON ((11.1635884 42.8290888, 11.1635884 42...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>38948</th>
      <td>2013-08-18T17:03:48Z</td>
      <td>The modern Acquapendente was settled in pre-Ro...</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Acquapendente</td>
      <td>https://pleiades.stoa.org/places/555723846</td>
      <td>555723846</td>
      <td>42.743996</td>
      <td>11.864988</td>
      <td>POLYGON ((11.864988 42.7439961, 11.864988 42.7...</td>
    </tr>
    <tr>
      <th>38949</th>
      <td>2018-04-30T00:12:40Z</td>
      <td>The Roman amphitheater at Bulla Regia</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Roman amphitheater at Bulla Regia</td>
      <td>https://pleiades.stoa.org/places/55568842</td>
      <td>55568842</td>
      <td>36.561892</td>
      <td>8.760049</td>
      <td>POLYGON ((8.760049 36.5618917, 8.760049 36.561...</td>
    </tr>
    <tr>
      <th>38950</th>
      <td>2020-05-14T21:57:48Z</td>
      <td>Gövelek is a village situated 27 km to the eas...</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Gövelek</td>
      <td>https://pleiades.stoa.org/places/555685926</td>
      <td>555685926</td>
      <td>38.532489</td>
      <td>43.620359</td>
      <td>POLYGON ((43.620359 38.5324889, 43.620359 38.5...</td>
    </tr>
    <tr>
      <th>38951</th>
      <td>2021-11-05T14:17:34Z</td>
      <td>The Thermae Helenianae or Thermae Helenae was ...</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Thermae Helenianae</td>
      <td>https://pleiades.stoa.org/places/555157618</td>
      <td>555157618</td>
      <td>41.889222</td>
      <td>12.514661</td>
      <td>POLYGON ((12.514661 41.889222, 12.514661 41.88...</td>
    </tr>
    <tr>
      <th>38952</th>
      <td>2021-02-14T18:15:06Z</td>
      <td>An unusual circular "fort" dating to the Old K...</td>
      <td>&lt;p&gt;According to Mumford 2006, the site was ide...</td>
      <td>Pleiades</td>
      <td>Tell Ras Budran</td>
      <td>https://pleiades.stoa.org/places/555858016</td>
      <td>555858016</td>
      <td>28.984232</td>
      <td>33.181649</td>
      <td>POLYGON ((33.1818643 28.9840031, 33.1818643 28...</td>
    </tr>
  </tbody>
</table>
<p>38953 rows × 10 columns</p>
</div>




```python
# let's find 'Rome' in places
places.loc[places['id'] == 423025]  #places.loc[places.['id'] == 423025
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>created</th>
      <th>description</th>
      <th>details</th>
      <th>provenance</th>
      <th>title</th>
      <th>uri</th>
      <th>id</th>
      <th>representative_latitude</th>
      <th>representative_longitude</th>
      <th>bounding_box_wkt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>21483</th>
      <td>2018-06-07T19:48:13Z</td>
      <td>The capital of the Roman Republic and Empire.</td>
      <td>&lt;p&gt;The Barrington Atlas Directory notes: Roma/...</td>
      <td>Barrington Atlas: BAtlas 43 B2 Roma</td>
      <td>Roma</td>
      <td>https://pleiades.stoa.org/places/423025</td>
      <td>423025</td>
      <td>41.891775</td>
      <td>12.486137</td>
      <td>POLYGON ((12.486137 41.891775, 12.486137 41.89...</td>
    </tr>
  </tbody>
</table>
</div>




```python
names = pd.read_csv('names.csv')
names.columns
```




    Index(['created', 'description', 'details', 'provenance', 'title', 'uri', 'id',
           'place_id', 'name_type', 'language_tag', 'attested_form',
           'romanized_form_1', 'romanized_form_2', 'romanized_form_3',
           'association_certainty', 'transcription_accuracy',
           'transcription_completeness', 'year_after_which', 'year_before_which'],
          dtype='object')




```python
# let's find 'Rome' in names
names.loc[names['romanized_form_1'] =='Rome'].place_id
```




    20810    423025
    Name: place_id, dtype: int64




```python
def get_pleiades_id(term):
    """
    Iterates through all of the possible names in the names.csv file
    Returns None if no matched names
    """
    name_row = names.loc[names['attested_form'] == term]
    if len(name_row) == 1:
        return int(name_row.place_id.iloc[0])
    else:
        name_row = names.loc[names['romanized_form_1'] == term]
        if len(name_row) == 1:
            return int(name_row.place_id.iloc[0])
        else:
            name_row = names.loc[names['romanized_form_2'] == term]
            if len(name_row) == 1:
                return int(name_row.place_id.iloc[0])
            else:
                name_row = names.loc[names['romanized_form_3'] == term]
                if len(name_row) == 1:
                    return int(name_row.place_id.iloc[0])
                else:
                    return None
```

The above function looks very complicated, but all it's doing is checking the results of several `loc` and `iloc` calls from our `DataFrame`. You will very rarely see `for` loops when using `pandas`. Instead, you will see programmers taking advantage of the very efficient search methods in `pandas` like `loc`, sometimes called 'broadcasting'. Check out this [Medium post](https://medium.com/@michaeleby1/broadcasting-versus-iteration-6c06539dc1d5) for a further discussion of the benefits of broadcasting over writing loops.


```python
place_freq_df['pleiades_id'] = place_freq_df['place_name'].apply(get_pleiades_id)
place_freq_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nerva</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Trajan</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Marcus Antoninus</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aethiopia</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>147</th>
      <td>Great Britain</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>148</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
    </tr>
    <tr>
      <th>149</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
    </tr>
    <tr>
      <th>150</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
    </tr>
    <tr>
      <th>151</th>
      <td>the Western Ocean</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>152 rows × 3 columns</p>
</div>



Why are there so many `NaN` values? How do we deal with them? How do we want to deal with them?
(NaN means not a number).  WANT TO GET RID OF NaNs.


```python
place_freq_final = place_freq_df.dropna().reset_index(drop=True)
place_freq_final
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Euphrates</td>
      <td>6</td>
      <td>912849.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabia</td>
      <td>2</td>
      <td>981506.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Africa</td>
      <td>7</td>
      <td>775.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ireland</td>
      <td>1</td>
      <td>20487.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>India</td>
      <td>1</td>
      <td>50004.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Iberia</td>
      <td>1</td>
      <td>863807.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Caledonia</td>
      <td>1</td>
      <td>89132.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Misenum</td>
      <td>2</td>
      <td>432941.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Naples</td>
      <td>3</td>
      <td>433014.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tarragona</td>
      <td>1</td>
      <td>246349.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Lugdunum</td>
      <td>1</td>
      <td>167717.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Hercules</td>
      <td>2</td>
      <td>266032.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illyricum</td>
      <td>2</td>
      <td>481865.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Macedonia</td>
      <td>3</td>
      <td>491656.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Augsburg</td>
      <td>1</td>
      <td>118580.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Thebes</td>
      <td>1</td>
      <td>541138.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Argos</td>
      <td>1</td>
      <td>570106.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sparta</td>
      <td>1</td>
      <td>570685.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Athens</td>
      <td>1</td>
      <td>579885.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Achaia</td>
      <td>1</td>
      <td>570028.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Troy</td>
      <td>1</td>
      <td>550595.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Ionia</td>
      <td>1</td>
      <td>550597.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Constantinople</td>
      <td>1</td>
      <td>520998.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Trebizond</td>
      <td>2</td>
      <td>857359.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Nile</td>
      <td>1</td>
      <td>727172.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Algiers</td>
      <td>1</td>
      <td>295276.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
    </tr>
  </tbody>
</table>
</div>



Now that we have the Pleiades IDs, we can finally get the coordiantes from `places.csv`. Let's look back at the Roman example. 


```python
rid = place_freq_final.pleiades_id.iloc[0]
places.loc[places['id'] == rid].representative_latitude.iloc[0] #21483
# rid = Rome id in pleiades.
#iloc is index location specifically.  
```




    41.891775




```python
# could've just one function here, but not too much trouble to do two
def get_lat(pl_id):
    places_row = places.loc[places['id'] == pl_id]
    if len(places_row) == 1:
        return places_row.representative_latitude.iloc[0]
    
def get_long(pl_id):
    places_row = places.loc[places['id'] == pl_id]
    if len(places_row) == 1:
        return places_row.representative_longitude.iloc[0]
```


```python
place_freq_final['lat'] = place_freq_final['pleiades_id'].apply(get_lat)
place_freq_final['long'] = place_freq_final['pleiades_id'].apply(get_long)
```


```python
place_freq_final
# run this cell and have completed dataframe. 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
      <th>lat</th>
      <th>long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
      <td>41.891775</td>
      <td>12.486137</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Euphrates</td>
      <td>6</td>
      <td>912849.0</td>
      <td>35.543310</td>
      <td>39.606018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabia</td>
      <td>2</td>
      <td>981506.0</td>
      <td>27.500000</td>
      <td>32.500000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Africa</td>
      <td>7</td>
      <td>775.0</td>
      <td>32.500000</td>
      <td>7.500000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ireland</td>
      <td>1</td>
      <td>20487.0</td>
      <td>53.184028</td>
      <td>-7.717526</td>
    </tr>
    <tr>
      <th>5</th>
      <td>India</td>
      <td>1</td>
      <td>50004.0</td>
      <td>22.500000</td>
      <td>77.500000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Iberia</td>
      <td>1</td>
      <td>863807.0</td>
      <td>41.836468</td>
      <td>44.689138</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Caledonia</td>
      <td>1</td>
      <td>89132.0</td>
      <td>57.500000</td>
      <td>-4.500000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Misenum</td>
      <td>2</td>
      <td>432941.0</td>
      <td>40.786279</td>
      <td>14.084884</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Naples</td>
      <td>3</td>
      <td>433014.0</td>
      <td>40.839995</td>
      <td>14.252870</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tarragona</td>
      <td>1</td>
      <td>246349.0</td>
      <td>41.116892</td>
      <td>1.258338</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Lugdunum</td>
      <td>1</td>
      <td>167717.0</td>
      <td>45.758866</td>
      <td>4.819482</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Hercules</td>
      <td>2</td>
      <td>266032.0</td>
      <td>37.500000</td>
      <td>-0.500000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illyricum</td>
      <td>2</td>
      <td>481865.0</td>
      <td>42.427292</td>
      <td>17.965028</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Macedonia</td>
      <td>3</td>
      <td>491656.0</td>
      <td>41.250000</td>
      <td>21.750000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Augsburg</td>
      <td>1</td>
      <td>118580.0</td>
      <td>48.365463</td>
      <td>10.894765</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Thebes</td>
      <td>1</td>
      <td>541138.0</td>
      <td>38.319156</td>
      <td>23.317577</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Argos</td>
      <td>1</td>
      <td>570106.0</td>
      <td>37.631561</td>
      <td>22.719464</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sparta</td>
      <td>1</td>
      <td>570685.0</td>
      <td>37.077905</td>
      <td>22.427298</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Athens</td>
      <td>1</td>
      <td>579885.0</td>
      <td>37.972634</td>
      <td>23.722746</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Achaia</td>
      <td>1</td>
      <td>570028.0</td>
      <td>38.102121</td>
      <td>22.224586</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Troy</td>
      <td>1</td>
      <td>550595.0</td>
      <td>39.957433</td>
      <td>26.238459</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Ionia</td>
      <td>1</td>
      <td>550597.0</td>
      <td>38.162194</td>
      <td>27.357498</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Constantinople</td>
      <td>1</td>
      <td>520998.0</td>
      <td>41.006371</td>
      <td>28.965352</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Trebizond</td>
      <td>2</td>
      <td>857359.0</td>
      <td>41.004269</td>
      <td>39.723312</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Nile</td>
      <td>1</td>
      <td>727172.0</td>
      <td>19.211409</td>
      <td>30.567330</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Algiers</td>
      <td>1</td>
      <td>295276.0</td>
      <td>36.768912</td>
      <td>3.053218</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
      <td>39.871087</td>
      <td>8.957517</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
      <td>37.643297</td>
      <td>13.932018</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
      <td>35.908887</td>
      <td>14.408042</td>
    </tr>
  </tbody>
</table>
</div>




```python
place_freq_final.to_csv('ch1gibbon_places.csv')
```

## In-class Activity

Now pair up and try to take all of the above steps and turn it into a single function, so that all you need to do is pass in the text of a chapter and you get back a DataFrame with place names and coordinates. To walkthrough the steps again:

* Consider what all of your inputs are
* Use `spaCy` syntax to parse input text
* Filter and count by place name and label
* Use `pandas` to manipulate the data into a useful form

Try out some different chapters. Do you notice anything about the places? Do the types of places or the accuracy of Pleiades get better or worse over the chapters? I encourage you to Google or look up any entities you don't know on Wikipedia.

## Homework


```python
import collections
```


```python
#make sure to run 'import collections'
#chapter = #some string ''
def get_pleiades_id(term):
    """
    Iterates through all of the possible names in the names.csv file
    Returns None if no matched names
    """
    name_row = names.loc[names['attested_form'] == term]
    if len(name_row) == 1:
        return int(name_row.place_id.iloc[0])
    else:
        name_row = names.loc[names['romanized_form_1'] == term]
        if len(name_row) == 1:
            return int(name_row.place_id.iloc[0])
        else:
            name_row = names.loc[names['romanized_form_2'] == term]
            if len(name_row) == 1:
                return int(name_row.place_id.iloc[0])
            else:
                name_row = names.loc[names['romanized_form_3'] == term]
                if len(name_row) == 1:
                    return int(name_row.place_id.iloc[0])
                else:
                    return None
               
               
def get_lat(pl_id):
    places_row = places.loc[places['id'] == pl_id]
    if len(places_row) == 1:
        return places_row.representative_latitude.iloc[0]
    
   
def get_long(pl_id):
    places_row = places.loc[places['id'] == pl_id]
    if len(places_row) == 1:
        return places_row.representative_longitude.iloc[0]
   
   
def get_locations(chapter, output_name):
    chapter_doc = nlp(chapter)
    place_freq = collections.defaultdict(int)
    for entity in first_chapter_doc.ents:
        if (entity.label_ == 'GPE') or (entity.label_ == 'LOC'):
            place_freq[entity.text] += 1 # the utility of defaultdict!
    place_freq = dict(place_freq)
    place_freq_df = pd.DataFrame.from_dict(place_freq, orient='index').reset_index().rename(columns={'index':'place_name',0:'frequency'})
    places = pd.read_csv('places.csv')
    names = pd.read_csv('names.csv')
    place_freq_df['pleiades_id'] = place_freq_df['place_name'].apply(get_pleiades_id)
    place_freq_final = place_freq_df.dropna().reset_index(drop=True)
    place_freq_final['lat'] = place_freq_final['pleiades_id'].apply(get_lat)
    place_freq_final['long'] = place_freq_final['pleiades_id'].apply(get_long)
    place_freq_final.to_csv(f'{output_name}.csv')
    return place_freq_final
```


```python
place_freq_final
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
      <th>lat</th>
      <th>long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
      <td>41.891775</td>
      <td>12.486137</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Euphrates</td>
      <td>6</td>
      <td>912849.0</td>
      <td>35.543310</td>
      <td>39.606018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabia</td>
      <td>2</td>
      <td>981506.0</td>
      <td>27.500000</td>
      <td>32.500000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Africa</td>
      <td>7</td>
      <td>775.0</td>
      <td>32.500000</td>
      <td>7.500000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ireland</td>
      <td>1</td>
      <td>20487.0</td>
      <td>53.184028</td>
      <td>-7.717526</td>
    </tr>
    <tr>
      <th>5</th>
      <td>India</td>
      <td>1</td>
      <td>50004.0</td>
      <td>22.500000</td>
      <td>77.500000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Iberia</td>
      <td>1</td>
      <td>863807.0</td>
      <td>41.836468</td>
      <td>44.689138</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Caledonia</td>
      <td>1</td>
      <td>89132.0</td>
      <td>57.500000</td>
      <td>-4.500000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Misenum</td>
      <td>2</td>
      <td>432941.0</td>
      <td>40.786279</td>
      <td>14.084884</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Naples</td>
      <td>3</td>
      <td>433014.0</td>
      <td>40.839995</td>
      <td>14.252870</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tarragona</td>
      <td>1</td>
      <td>246349.0</td>
      <td>41.116892</td>
      <td>1.258338</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Lugdunum</td>
      <td>1</td>
      <td>167717.0</td>
      <td>45.758866</td>
      <td>4.819482</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Hercules</td>
      <td>2</td>
      <td>266032.0</td>
      <td>37.500000</td>
      <td>-0.500000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illyricum</td>
      <td>2</td>
      <td>481865.0</td>
      <td>42.427292</td>
      <td>17.965028</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Macedonia</td>
      <td>3</td>
      <td>491656.0</td>
      <td>41.250000</td>
      <td>21.750000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Augsburg</td>
      <td>1</td>
      <td>118580.0</td>
      <td>48.365463</td>
      <td>10.894765</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Thebes</td>
      <td>1</td>
      <td>541138.0</td>
      <td>38.319156</td>
      <td>23.317577</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Argos</td>
      <td>1</td>
      <td>570106.0</td>
      <td>37.631561</td>
      <td>22.719464</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sparta</td>
      <td>1</td>
      <td>570685.0</td>
      <td>37.077905</td>
      <td>22.427298</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Athens</td>
      <td>1</td>
      <td>579885.0</td>
      <td>37.972634</td>
      <td>23.722746</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Achaia</td>
      <td>1</td>
      <td>570028.0</td>
      <td>38.102121</td>
      <td>22.224586</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Troy</td>
      <td>1</td>
      <td>550595.0</td>
      <td>39.957433</td>
      <td>26.238459</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Ionia</td>
      <td>1</td>
      <td>550597.0</td>
      <td>38.162194</td>
      <td>27.357498</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Constantinople</td>
      <td>1</td>
      <td>520998.0</td>
      <td>41.006371</td>
      <td>28.965352</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Trebizond</td>
      <td>2</td>
      <td>857359.0</td>
      <td>41.004269</td>
      <td>39.723312</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Nile</td>
      <td>1</td>
      <td>727172.0</td>
      <td>19.211409</td>
      <td>30.567330</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Algiers</td>
      <td>1</td>
      <td>295276.0</td>
      <td>36.768912</td>
      <td>3.053218</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
      <td>39.871087</td>
      <td>8.957517</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
      <td>37.643297</td>
      <td>13.932018</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
      <td>35.908887</td>
      <td>14.408042</td>
    </tr>
  </tbody>
</table>
</div>




```python
get_locations(gibbon_by_chapter['StringText'][38], 'gibbon_chapter_38_places')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
      <th>lat</th>
      <th>long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
      <td>41.891775</td>
      <td>12.486137</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Euphrates</td>
      <td>6</td>
      <td>912849.0</td>
      <td>35.543310</td>
      <td>39.606018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabia</td>
      <td>2</td>
      <td>981506.0</td>
      <td>27.500000</td>
      <td>32.500000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Africa</td>
      <td>7</td>
      <td>775.0</td>
      <td>32.500000</td>
      <td>7.500000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ireland</td>
      <td>1</td>
      <td>20487.0</td>
      <td>53.184028</td>
      <td>-7.717526</td>
    </tr>
    <tr>
      <th>5</th>
      <td>India</td>
      <td>1</td>
      <td>50004.0</td>
      <td>22.500000</td>
      <td>77.500000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Iberia</td>
      <td>1</td>
      <td>863807.0</td>
      <td>41.836468</td>
      <td>44.689138</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Caledonia</td>
      <td>1</td>
      <td>89132.0</td>
      <td>57.500000</td>
      <td>-4.500000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Misenum</td>
      <td>2</td>
      <td>432941.0</td>
      <td>40.786279</td>
      <td>14.084884</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Naples</td>
      <td>3</td>
      <td>433014.0</td>
      <td>40.839995</td>
      <td>14.252870</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tarragona</td>
      <td>1</td>
      <td>246349.0</td>
      <td>41.116892</td>
      <td>1.258338</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Lugdunum</td>
      <td>1</td>
      <td>167717.0</td>
      <td>45.758866</td>
      <td>4.819482</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Hercules</td>
      <td>2</td>
      <td>266032.0</td>
      <td>37.500000</td>
      <td>-0.500000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illyricum</td>
      <td>2</td>
      <td>481865.0</td>
      <td>42.427292</td>
      <td>17.965028</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Macedonia</td>
      <td>3</td>
      <td>491656.0</td>
      <td>41.250000</td>
      <td>21.750000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Augsburg</td>
      <td>1</td>
      <td>118580.0</td>
      <td>48.365463</td>
      <td>10.894765</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Thebes</td>
      <td>1</td>
      <td>541138.0</td>
      <td>38.319156</td>
      <td>23.317577</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Argos</td>
      <td>1</td>
      <td>570106.0</td>
      <td>37.631561</td>
      <td>22.719464</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sparta</td>
      <td>1</td>
      <td>570685.0</td>
      <td>37.077905</td>
      <td>22.427298</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Athens</td>
      <td>1</td>
      <td>579885.0</td>
      <td>37.972634</td>
      <td>23.722746</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Achaia</td>
      <td>1</td>
      <td>570028.0</td>
      <td>38.102121</td>
      <td>22.224586</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Troy</td>
      <td>1</td>
      <td>550595.0</td>
      <td>39.957433</td>
      <td>26.238459</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Ionia</td>
      <td>1</td>
      <td>550597.0</td>
      <td>38.162194</td>
      <td>27.357498</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Constantinople</td>
      <td>1</td>
      <td>520998.0</td>
      <td>41.006371</td>
      <td>28.965352</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Trebizond</td>
      <td>2</td>
      <td>857359.0</td>
      <td>41.004269</td>
      <td>39.723312</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Nile</td>
      <td>1</td>
      <td>727172.0</td>
      <td>19.211409</td>
      <td>30.567330</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Algiers</td>
      <td>1</td>
      <td>295276.0</td>
      <td>36.768912</td>
      <td>3.053218</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
      <td>39.871087</td>
      <td>8.957517</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
      <td>37.643297</td>
      <td>13.932018</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
      <td>35.908887</td>
      <td>14.408042</td>
    </tr>
  </tbody>
</table>
</div>




```python
place_freq_final.to_csv('ch38gibbon_places.csv')
# This file now appears in learn_python folder.
```
