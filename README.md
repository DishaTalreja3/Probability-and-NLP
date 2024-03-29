# B551 Assignment 3: Probability and Statistical Learning for NLP
##### Submission by Sri Harsha Manjunath - srmanj@iu.edu; Vijayalaxmi Bhimrao Maigur - vbmaigur@iu.edu; Disha Talreja - dtalreja@iu.edu
###### Fall 2019


## Part 1: Part-of-speech Tagging

Natural language processing (NLP) is an important research area in artificial intelligence, dating back to at least the 1950's. A basic problems in NLP is part-of-speech tagging, in which the goal is to mark every word in a sentence with its part of speech (noun, verb, adjective, etc.). This is a first step towards extracting semantics from natural language text. For example, consider the following sentence: \Her position covers a number of daily tasks common to any social director." Part-of-speech tagging here is not easy because many of these words can take on different parts of speech depending on context. For example, position can be a noun (as in the above sentence) or a verb (as in \They position themselves near the exit"). In fact, covers, number, and tasks can all be used as either nouns or verbs, while social and common can be nouns
or adjectives, and daily can be an adjective, noun, or adverb. The correct labeling for the above sentence is: Her position covers a number of daily tasks common to any social director. DET NOUN VERB DET NOUN ADP ADJ NOUN ADJ ADP DET ADJ NOUN where DET stands for a determiner, ADP is an adposition, ADJ is an adjective, and ADV is an adverb.1 Labeling parts of speech thus involves an understanding of the intended meaning of the words in the sentence, as well as the relationships between the words.

Fortunately, statistical models work amazingly well for NLP problems. Consider the Bayes net shown in Figure 1(a). This Bayes net has random variables 
S = {S1, . . . , SN} and W = {W1, . . . .WN}. The W's represent observed words in a sentence. The S's represent part of speech tags, so Si = {VERB,NOUN, . . .} The arrows between W and S nodes model the relationship between a given observed word and the possible parts of speech it can take on, P(Wi|Si). (For example, these distributions can model the fact that the word \dog" is a fairly common noun but a very rare verb.) The arrows between S nodes model the probability that a word of one part of speech follows a word of another part of speech, P(Si+1|Si). (For example, these arrows can model the fact that verbs are very likely to follow nouns, but are unlikely to follow adjectives.)

### Solution:

The problem is modelled as bayes net with
1. Hidden Variable : Parts of Speech Label for every word in a sentence
2. Observed Variable: Words in a sentence

#### Approach 1: Simple Bayes Net:

![alt text](https://github.iu.edu/cs-b551-fa2019/vbmaigur-srmanj-dtalreja-a3/blob/master/images/simple_bayes_net_1.png)

Training Part: Here, We have to find two argmax(P(S</sup><sub>i</sub>|W</sup><sub>i</sub>)) equals to P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)*P(S</sup><sub>i</sub>)

##### P(S</sup><sub>i</sub>):
- DataType: dictionary
- Key: Part of Speech(POS)
- Value: Probability value of the POS
    - ###### Probability = number of occurance of POS / number of words
- Length: Number of predefined POS
- Name of Variable in code: self.initial_prob

```
{'adj': 0.06563706563706563,
 'adv': 0.023166023166023165,
 'adp': 0.11583011583011583,
 'conj': 0.03861003861003861,
 'det': 0.13513513513513514,
 'noun': 0.2702702702702703,
 'num': 0.003861003861003861,
 'pron': 0.023166023166023165,
 'prt': 0.011583011583011582,
 'verb': 0.1776061776061776,
 'x': 0.0,
 '.': 0.13513513513513514}
 ```

##### P(W</sup><sub>i</sub>|S</sup><sub>i</sub>):

- DataType: dictionary
- Key: Part of Speech(POS)
- Value: dictionary
    - Key: Word
    - Value: Probability of the word given the POS
        - ###### Probability = number of occurance of that word as POS / number of occurance of POS
- Length: Number of predefined POS
- Name of Variable in code: self.emiision_prob

```
{'adj': 
{'executive': 0.058823529411764705,
  'over-all': 0.058823529411764705,
  'superior': 0.058823529411764705,
  'possible': 0.058823529411764705,
  'hard-fought': 0.058823529411764705,
  'relative': 0.058823529411764705,
  'such': 0.058823529411764705,
  'widespread': 0.058823529411764705,
  'many': 0.058823529411764705,
  'outmoded': 0.058823529411764705,
  'inadequate': 0.058823529411764705,
  'ambiguous': 0.058823529411764705,
  'grand': 0.058823529411764705,
  'other': 0.058823529411764705,
  'best': 0.058823529411764705,
  'greater': 0.058823529411764705,
  'clerical': 0.058823529411764705}}
```

Prediction on Test Data: 
```
- for every word in a sentence
    - for every POS
        - find P(S</sup><sub>i</sub>|W</sup><sub>i</sub>)=P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)*P(S</sup><sub>i</sub>)
    - the prediction for the word is the pos with max probability for the word
```

Finding the posterior, given a sentence and corresponding label

- sum of P(S</sup><sub>i</sub>|W</sup><sub>i</sub>) of all (word, pos) pair for a sentence

#### Approach 2: HMM Viterbi Algorithm:

![alt text](https://github.iu.edu/cs-b551-fa2019/vbmaigur-srmanj-dtalreja-a3/blob/master/images/hmm_viterbi.png)

Training Part: Here, We have to find two P(S</sup><sub>i</sub>|W</sup><sub>i</sub>) equals to P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)*argmax(P(previous_state)*P(S</sup><sub>i+1</sub>|S</sup><sub>i</sub>))

##### P(W</sup><sub>i</sub>|S</sup><sub>i</sub>):

- explained above

##### P(S</sup><sub>i+1</sub>,S</sup><sub>i</sub>):

- DataType: dictionary
- Key: Part of Speech(POS) (state t-1)
- Value: dictionary
    - Key: POS (state t)
    - Value: Probability of the word given the POS
        - ###### Probability = number of occurance of POS at t-1 and t / number of joint occurance of POS
- Length: Number of predefined POS
- Name of Variable in code: self.transition_prob

```
{'adj': {'noun': 0.048, '.': 0.008, 'adp': 0.004, 'conj': 0.008},
 'adv': {'verb': 0.012, 'det': 0.004, 'adj': 0.004, '.': 0.004}}
 ```

 ##### P(previous_state):

 - Probability of the previous being a particular POS (no need to calculate using traning data handled in the implementation of algo)


 Prediction on Test Data:
```
 - for word in a sentence:
    - for every pos
        - prob of first word being pos is P(W</sup><sub>i</sub>|S</sup><sub>i</sub>) (store all values in a dictionary with key as pos and value is a tuple( prob, path_traversed_till_now))

        - prob of every other word other than first:
        - for every prev_pos
            - product of Transition probability and previous state prob
            - store in a different dict 
        - take the max of the above prob and multiply with emission probability
- take the max prob of the last word and the path attached to it
```

Finding the posterior, given a sentence and corresponding label

- sum of P(S</sup><sub>i</sub>|W</sup><sub>i</sub>)=P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)* P(S</sup><sub>i+1</sub>|S</sup><sub>i</sub>) of all (word, pos) pair for a sentence


#### Simple illustration of the hmm-viterbi algorithm for POS
##### [ref]-http://www.phontron.com/slides/nlp-programming-en-04-hmm.pdf

![alt text](https://github.iu.edu/cs-b551-fa2019/vbmaigur-srmanj-dtalreja-a3/blob/master/images/re.png)

#### Approach 3: Gibbs Sampling:

![alt text](https://github.iu.edu/cs-b551-fa2019/vbmaigur-srmanj-dtalreja-a3/blob/master/images/gibbs.png)

Training Part: Here, We have to find two P(S</sup><sub>i</sub>|W</sup><sub>i</sub>) equals to P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)*argmax(P(previous_state)*P(S</sup><sub>i+1</sub>|S</sup><sub>i</sub>))

##### P(W</sup><sub>i</sub>|S</sup><sub>i</sub>):

- explained above

##### P(S</sup><sub>i+1</sub>,S</sup><sub>i</sub>):

- explained above

##### P(S</sup><sub>n</sub>|S</sup><sub>0</sub>):

- DataType: dictionary
- Key: Part of Speech(POS) (state 0)
- Value: dictionary
    - Key: POS (state n)
    - Value: Probability of the word given the POS
        - ###### Probability = number of occurance of POS at t-1 and t / number of joint occurance of POS
- Length: Number of predefined POS
- Name of Variable in code: self.gibbs_transition_prob

```
{'adj': {},
 'adv': {'.': 0.1111111111111111},
 'adp': {},
 'conj': {},
 'det': {'.': 0.5555555555555556},
 'noun': {'verb': 0.1111111111111111},
 'num': {},
 'pron': {'.': 0.1111111111111111},
 'prt': {},
 'verb': {},
 'x': {},
 '.': {'.': 0.1111111111111111}}
```

Prediction on Test Data:

```
- first randomly initialized state is output of simple bayes net
- gibbs_algo, genearates the sample label based on the label that is passed to the function
- gibbs algo function
    - for every word 
        - for every pos
            - use the same implementation as hmm viterbi to calculate the prob of all words expect last word
            - for last word prob multiply extra gibbs transition prob according to the given bayes net
        
        random distribution is used to select a pos for a word
    - return the sample
- complex function
    - burn out first few iterations~ 200
    - collect the samples for around 500 iteration 
    - the predicted pos is the one with high frequency in 500 samples for that word.

```

Finding the posterior, given a sentence and corresponding label

- sum of P(S</sup><sub>i</sub>|W</sup><sub>i</sub>)=P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)* P(S</sup><sub>i+1</sub>|S</sup><sub>i</sub>) of all (word, pos) pair for a sentence except last word
- for last word it is P(S</sup><sub>i</sub>|W</sup><sub>i</sub>)=P(W</sup><sub>i</sub>|S</sup><sub>i</sub>)* P(S</sup><sub>i+1</sub>|S</sup><sub>i</sub>) * P(S</sup><sub>n</sub>|S</sup><sub>0</sub>)

#### Assumption:

1. To numerical instability, we have considered log of the probabilities in our calculation
2. In the absence of any probability value, we have used very small prob to avoid log(0) error.
3. In finding transition probability during implementing the algo, we have used the below formula
    P(S</sup><sub>i+1</sub>|S</sup><sub>i</sub>)=P(S</sup><sub>i+1</sub>,S</sup><sub>i</sub>)/P(S</sup><sub>i</sub>)
    

#### Accuracy:

![alt text](https://github.iu.edu/cs-b551-fa2019/vbmaigur-srmanj-dtalreja-a3/blob/master/images/Accuracy.png)

**References** - </br>

[1] - https://www.freecodecamp.org/news/a-deep-dive-into-part-of-speech-tagging-using-viterbi-algorithm-17c8de32e8bc/

## Part 2: Code Breaking

You've intercepted a secret message that is encrypted using both of two techniques. In Replacement, each letter of the alphabet is replaced with another letter of the alphabet. (For example, all a's may have been replaced with f's, b's with z's, etc.) Unfortunately, we don't know the mapping that was used. In Rearrangement, the order of the characters is scrambled. For each consecutive sequence of n characters, the characters are reordered according to a function that maps character indices to character indices. For example, if n = 4 and the mapping function is f(0) = 2; f(1) = 0; f(2) = 1; f(3) = 3, then the string test would be rearranged to be estt (because the character at index 0 was moved to index 2, index 1 was moved to index 0, etc). We don't know the mapping function that the encoder used, but we do know that n = 4. How can we decrypt a document without knowing the encryption tables? Probabilistic methods come to the
rescue. For any given sequence of characters, we can score how \English-like" it is by viewing language as simple a Markov chain over letters of the alphabet. We can define the probability that a document D was generated from the English language, P(D) = Qi P(Wi); where Wi is the i-th word of the document, and

Now let's say we randomly applied different decryption
tables to an encrypted document. For each of those candidate decryptions D, we can calculate P(D), and
then choose the one that is highest | the one that maximizes the likelihood of the data.

Unfortunately, trying all possible tables is impossible because the number of possible codes is unthinkably
enormous | there are 26! possible replacement code books and 4! possible rearrangement codes, for a
total of about 10 trillion quadrillion combinations. Here's an alternative, based on something called the
Metropolis-Hastings algorithm:
1. Start with a guess about the encryption tables. Call the guess T.
2. Modify T to produce a new guess, T0. The modification could be switching two letters in one of the
tables, for example.
3. Decrypt the encoded document using T to produce document D, and decrypt the document using T0
to produce D0.
4. If P(D0) > P(D); then replace T with T0. Otherwise, with probability P(D0)
P(D) , replace T with T0.
5. Go to step 2.

Write a program to break codes of the above type. Your program should be run like this:

```
./break_code.py encoded_document english_corpus output
```

where encoded document is the name of a encrypted file, english corpus is a document containing some
English text, and output is the file in which to write the final decrypted output. The purpose of the English
corpus is to estimate the probabilities needed to compute the probabilities above. (Intuitively, the idea is
that your program is searching for a code such that, when used to decrypt the document, the statistics of
the decrypted document match the statistics of known English text.)
Your code should output the best possible decryption it can find within a time limit of about 10 minutes.
You might want to consider variations on the above algorithm to improve performance, such as running the
algorithm multiple times and using the best (highest-probability) result. Make sure to explain these design
decisions in your report. For simplicity, you can assume that the input and output documents consist only
of lowercase letters and spaces - no punctuation or capital letters.

## Solution 


The solution has the following parts that were created - 

#### 1. Generating Initial Probabilities - 

To compute the P(W<sup>0</sup><sub>i</sub>) and P(W<sup>j+1</sup><sub>i</sub>) for all j, 2 dictionaries were created W0 and W1.
 - W0 consists of all the 26 characters and space initialized to 0
 - W1 consists of all 27*27 combinations also initialized to 0

We the count the number of times a combination occurred and stored them against that combination in the dictionary and then calculate the probabilities as 
```
P(number of times 'vf' occurred)/P(Sum of all possibilities)
```

#### 2. Scoring a file using the generated probabilities

The entire document is split into individual words and the probability of each word is computed as follows

P(W0) * P(W1|W0) * P(W2|W1) * P(Wn|Wn-1)

Sidenote - We tried splitting the entire document into words/segments of 4 characters and computed the probabilities on those. This did not give accurate picture of what can be considered "English-like" because of all the spaces in the document.

####  3. Generate Rearrangement & Replacement
Given a dictionary or list of rearrangement or replacement table, randomly pick 2 
indices and flip them 

####  4. Break Code

- Start with a random replacement and rearrangement table
- Compute the score for this combination of replacement and rearrangement tables
- To induce as much randomness as possible we alternatively choose to flip either the rearrangement table or replacement table based on a uniform distribution
- Regardless of what was changed, a score is computed and checked against the best score seen so far
- If it is greater than the best score, it is accepted set as the best replacement and rearrangement table seen so far
- If not then it is accepted with a probability P(D*)/P(D)

Also, the code terminates when the last 1000 iterations see no change accepted and have completed 20k iterations

## Alternative Approaches tried
We also tried generating scores for 24 possible permutations of the rearrangement table for each random replace table and followed the same implementation for the rest of the approach.

It was observed that although this approach did yield acceptable results it took a very long amount of time. Around 35 minutes for 20,000 iterations. 
Also, the implementation results in a good encryption around 6 out of 10 times. If when evaluating the implementation, it results in a poor encryption do give it another run.

## Outputs

#### Encrypted Text 1
```
hen in the ourse of human events it becomes necessary for one people to dissolve the political bands which have connected them with another and to assume among the powers of the earth the separate and equal station to which the aws of ature and of atures od entitle them a decent respect to the opinions of mankind requires that they should declare the causes which impel them to the separation e hold these truths to be selfevident that all men are created equal that they are endowed by their reator with certain unalienable ights that among these are ife iberty and the pursuit of appinesshat to secure these rights overnments are instituted among en deriving their just powers from the consent of the governed hat whenever any orm of overnment becomes destructive of these ends it is the ight of the eople to alter or to abolish it and to institute new overnment laying its foundation on such principles and organizing its powers in such form as to them shall seem most likely to effect their afety and appiness rudence indeed will dictate that overnments long established should not be changed for light and transient causes and accordingly all experience hath shewn that mankind are more disposed to suffer while evils are sufferable than to right themselves by abolishing the forms to which they are accustomed ut when a long train of abuses and usurpations pursuing invariably the same bject evinces a design to reduce them under absolute espotism it is their right it is their duty to throw off such overnment and to provide new uards for their future securityuch has been the patient sufferance of these olonies and such is now the necessity which constrains them to alter their former ystems of overnment he history of the present ing of reat ritain is a history of repeated injuries and usurpations all having in direct object the establishment of an absolute yranny over these tates o prove this let acts be submitted to a candid world  e has refused his ssent to aws the most wholesome and necessary for the public good  e has forbidden his overnors to pass aws of immediate and pressing importance unless suspended in their operation till his ssent should be obtained and when so suspended he has utterly neglected to attend to them  e has refused to pass other aws for the accommodation of large districts of people unless those people would relinquish the right of epresentation in the egislature a right inestimable to them and formidable to tyrants only  e has called together legislative bodies at places unusual uncomfortable and distant from the depository of their public ecords for the sole purpose of fatiguing them into compliance with his measures  e has dissolved epresentative ouses repeatedly for opposing with manly firmness his invasions on the rights of the people  e has refused for a long time after such dissolutions to cause others to be elected whereby the egislative powers incapable of nnihilation have returned to the eople at large for their exercise the tate remaining in the mean time exposed to all the dangers of invasion from without and convulsions within  e has endeavoured to prevent the population of these tates for that purpose obstructing the aws for aturalization of oreigners refusing to pass others to encourage their migrations hither and raising the conditions of new ppropriations of ands  e has obstructed the dministration of ustice by refusing his ssent to aws for establishing udiciary powers  e has made udges dependent on his ill alone for the tenure of their offices and the amount and payment of their salaries  e has erected a multitude of ew ffices and sent hither swarms of fficers to harrass our people and eat out their substance  e has kept among us in times of peace tanding rmies without the onsent of our legislatures  e has affected to render the ilitary independent of and superior to the ivil power  e has combined with others to subject us to a jurisdiction foreign to our constitution and unacknowledged by our laws giving his ssent to their cts of pretended egislation  or uartering large bodies of armed troops among us or protecting them by a mock rial from punishment for any urders which they should commit on the nhabitants of these tates  or cutting off our rade with all parts of the world  or imposing axes on us without our onsent  or depriving us in many cases of the benefits of rial by ury  or transporting us beyond eas to be tried for pretended offences  or abolishing the free ystem of nglish aws in a neighbouring rovince establishing therein an rbitrary government and enlarging its oundaries so as to render it at once an example and fit instrument for introducing the same absolute rule into these olonies  or taking away our harters abolishing our most valuable aws and altering fundamentally the orms of our overnments  or suspending our own egislatures and declaring themselves invested with power to legislate for us in all cases whatsoever  e has abdicated overnment here by declaring us out of his rotection and waging ar against us  e has plundered our seas ravaged our oasts burnt our towns and destroyed the lives of our people  e is at this time transporting large rmies of foreign ercenaries to compleat the works of death desolation and tyranny already begun with circumstances of ruelty  perfidy scarcely paralleled in the most barbarous ages and totally unworthy the ead of a civilized nation  e has constrained our fellow itizens taken aptive on the high eas to bear rms against their ountry to become the executioners of their friends and rethren or to fall themselves by their ands  e has excited domestic insurrections amongst us and has endeavoured to bring on the inhabitants of our frontiers the merciless ndian avages whose known rule of warfare is an undistinguished destruction of all ages sexes and conditions  n every stage of these ppressions e have etitioned for edress in the most humble terms ur repeated etitions have been answered only by repeated injury  rince whose character is thus marked by every act which may define a yrant is unfit to be the ruler of a free people  or have e been wanting in attentions to our rittish brethren e have warned them from time to time of attempts by their legislature to extend an unwarrantable jurisdiction over us e have reminded them of the circumstances of our emigration and settlement here e have appealed to their native justice and magnanimity and we have conjured them by the ties of our common kindred to disavow these usurpations which would inevitably interrupt our connections and correspondence hey too have been deaf to the voice of justice and of consanguinity e must therefore acquiesce in the necessity which denounces our eparation and hold them as we hold the rest of mankind nemies in ar in eace riends  e therefore the epresentatives of the united tates of merica in eneral ongress ssembled appealing to the upreme udge of the world for the rectitude of our intentions do in the ame and by uthority of the good eople of these olonies solemnly publish and declare hat these nited olonies are and of ight ought to be ree and ndependent tates that they are bsolved from all llegiance to the ritish rown and that all political connection between them and the tate of reat ritain is and ought to be totally dissolved and that as ree and ndependent tates they have full ower to levy ar conclude eace contract lliances establish ommerce and to do all other cts and hings which ndependent tates may of right do nd for the support of this eclaration with a firm reliance on the protection of divine rovidence we mutually pledge to each other our ives our ortunes and our sacred onor
```

#### Encrypted Text 2

```
onures as the term is used by aviculturists include only the genera ratinga and yrrhura as well as several singlespecies genera and one doublespecies genus hese other genera are listed below onuropsis arolina parakeet extinct yanoliseus atagonian conure nicognathus austral and slenderbilled conures uarouba golden or ueen f avaria conure eptosittaca goldenplumed conure andayus  nanday conureno longer in its separate species andayusbut now an ratinga species gnorhynchus yelloweared conure ratingaedit ld upi for bright macaw ara  macaw tinga bright the ratinga conures generally seem to have a more mischievous personality than the real little macaws or minimacaws opular as pets the ratinga conures are generally larger with brighter plumage and are generally the noisier more outgoing more demanding of the two primary conure genera he colorful sun conure and jenday conure are among the species of conures more commonly kept as pets any of the ratinga species can be quite loud but otherwise can make very good pets for responsible owners ome species such as the dusky conure brown headed conure peach front conure and half moon conure are among the quieter ratinga species though they may still be loud at times ifespan is  years ee cherryheaded conure bluecrowned conure yrrhuraedit   family of greencheeked conures yrrhura is the other prominent genus of conures hese conures with generally more green colors include the very common greencheeked conure as well as the maroonbellied conure pearly conure blackcapped conure painted conure crimsonbellied conure and a number of other species hey are usually smaller have more subdued colors and are much quieter than the ratinga conures yrrhura species are growing in popularity as pet birds primarily due to their quiet nature their affectionate and intelligent personalities and the increasing number of color mutations developed in several of these species n average yrrhura conures live around  or  years o reach full maturity many of them molt to rid themselves of feathers from previous growth stages and occasionally young birds pluck their feathers during the winter anday conureedit  air of wild nanday conures he nanday conure nandayus nenday is the most commonly kept pet conure species outside the two main genera ome experts believe that nandays should actually be grouped with the genus ratinga since they are crossfertile with such species as jendays and suns anday conures have a distinctive black head and wings and tails tipped with dark blue feathers hey have a lightblue scarf and bright orange feathers on their legs and around their vents he maturity of a nanday can be told by the edges of its black hood if the hood has a ragged edge of brown then the bird is over a year old andays are often extremely noisythey are a heavily flockoriented species used to making their demands known calling out warnings for the group and calling to members of the group who are out of sight hey are also extremely social and intelligent birds capable of learning tricks mimicking sounds and learning a decent vocabulary t least one report suggests that they are highly adaptable to human encroachment on their territories but the exact status of the species in the wild is unknown locks of nanday conures live wild in parts of lorida notably the west coast including areas of t etersburg and learwater dditionally locks of wild anday conures ive in iesta ey arasota lorida  large flock of nanday conures lives wild in the acific alisades neighborhood of os ngeles hey have been in the area for the past several years olden conureedit he golden conure or ueen of avaria conure uaruba guarouba recently reclassified from ratinga guarouba is as the name implies covered all over with bright yellow feathers except for the green wingtip feathers and the greyish horncolored beak olden conures are among the most expensive conures both to purchase and to care for although many owners feel that the benefits outweigh the cost t is one of the rarest conures in the wild in addition to the pet trade any experts believe that these birds should not be kept in captivity unless in a breeding program atagonian conureedit  atagonian conure yanoliseus patagonus he atagonian conure also known as the burrowing parrot yanoliseus patagonus is the largest conure t can be found in the atagonia region of southcentral rgentina and hile rab on the top brightly colored underneath the atagonian conure has increased in popularity since the s leading to an increase in illegal importation that threatens wild populations however they are fairly commonly bred in captivity atagonian conures have a reputation for being exceptionally sweetnatured and intelligent conures but can be very loud and destructive with their chewing he greater atagonian conure yanoliseus patagonus bloxami or yanoliseus patagonus byroni is the largest of all the conures hey learn to talk and they usually bond very well with people especially if handraised and although not the most colorful bird they make great pets nicognathusedit omposed of the austral conure nicognathus ferrugineus which is native to the southern tip of outh merica and the slenderbilled conure nicognathus leptorhynchus endemic to hile oldenplumed conureedit he goldenplumed conure also called the goldenplumed parakeet eptosittaca branickii is a small ndean conure not found in aviculture and endangered in its own habitat elloweared conureedit he exceedingly rare yelloweared conure or gnorhynchus icterotis of olombia and cuador was never common in aviculture and has not successfully bred in captivity arolina parakeetedit onuropsis carolinensis the arolina parakeet was one of only two parrot species endemic to the nited tates in recorded history he arolina parakeet was a remarkably social bird living in vast flocks merican bird hunters reported that arolina parakeets would return to mourn dead members of the flock making themselves easy targets onsidered a pest popular in the pet trade and bearing plume feathers valued for hats this species was hunted to extinction around the beginning of the s oldcapped conureedit oldcapped conures ratinga auricapillusalso known as goldencapped parakeetsare native to razil and araguay heir natural habitats include subtropical or tropical dry forests subtropical or tropical moist lowland forests dry savanna and plantations he existence of this conure is threatened by habitat loss owever goldcapped conures are prolific breeders making them popular birds in aviculture and handfed golden cap babies are generally available he goldcapped conure grows to about  to  inches in length and weighs about  grams heir bodies are mainly green with blackish bills gray feet and brown irises he forehead the areas around the eyes and underwing coverts are usually red he breast feathers are a red and green mix heir tail feathers are olive green with a bluish tip he primary feathers wing coverts and underwing coverts are blue mmature goldcapped conures are mostly green with some orange around the eyes above the beak and on the breast near the wings ome navy is mixed in with the green of the tail he birds full coloration is not seen until they are sexually mature at about two years of age heir lifespan is about  years

```

#### Encrypted Text 3

```
o  union is more profound than marriage for it embodies the highest ideals of love fidelity devotion sacrifice and family n forming a marital union two people become something greater than once they were s some of the petitioners in these cases demonstrate marriage embodies a love that may endure even past death t would misunderstand these men and women to say they disrespect the idea of marriage heir plea is that they do respect it respect it so deeply that they seek to find its fulfillment for themselves heir hope is not to be condemned to live in loneliness excluded from one of civilizations oldest institutions hey ask for equal dignity in the eyes of the law he onstitution grants them that right he judgment of the ourt of ppeals for the ixth ircuit is reversed t is so ordered
```

#### Encrypted Text 4

```
he illy ibrary located on the campus of ndiana niversity in loomington ndiana is an important rare book and manuscript library in the nited tates t its dedication on ctober   the library contained a collection of  books  manuscripts more than fifty oil paintings and  prints urrently the illy ibrary has  million manuscripts  books  comic books  mini books  puzzles and  sheets of music  he illy ibrary was founded in  with the collection of osiah  illy r owner of illy harmaceuticals in ndianapolis  illy was a collector most of his life rom the mids until his death he devoted a great deal of his leisure time to building his collections of books and manuscripts works of art coins stamps military miniatures firearms and edged weapons and nautical models   illys collections of books and manuscripts totaling more than  books and  manuscripts together with more than fifty oil paintings and  prints were given by the collector to ndiana niversity between  and  hese materials form the foundation of the rare book and manuscript collections of the illy ibrary he extent to which   illy realized his collecting goals was summed up by rederick  dams r at the dedication of the illy ibrary on ctober   r illys books cover so many fields that it is difficult to believe that any one mans enthusiasm could encompass them all t is equally astounding that he was able to acquire so many books of such scarcity and quality in the short space of  years oney alone isnt the answer diligence courage and imagination were also essential he famous books in nglish and merican literature the books most influential in merican life the great works in the history of science and ideasall these are among the  illy books in this building  ollectionedit  ale niversitys copy of the unlap broadside he library now contains approximately  books  million manuscripts  comic books  mini books  puzzles and  pieces of sheet music he librarys holdings are particularly strong in ritish and merican history and literature atin mericana medicine and science food and drink childrens literature fine printing and binding popular music medieval and enaissance manuscripts and early printing here are many notable items in the librarys collections ew estament of the utenberg ible he first printed collection of hakespeares works the irst olio udubons irds of merica ne of  extant copies of the irst rinting of the eclaration of ndependence also known as the unlap roadside that was printed in hiladelphia on uly   eorge ashingtons letter accepting the presidency of the nited tates braham incolns desk from his law office and a leaf from the famous braham incoln um ook ca  ord hesterfields letters to his son he manuscripts of obert urnss uld ang yne he oxer odex manuscript written c  which contains illustrations of ethnic groups in the hilippines at the time of their initial contact with the paniards   ynges he layboy of the estern orld   arries eter an ypescripts of many of an lemings ames ond novels he library also owns the papers of ollywood directors rson elles and ohn ord film critic auline ael the poets ylvia lath and zra ound and authors dith harton and pton inclair uth  domeit collectionedit fter the death of uth  domeit in  her collection of miniature books was donated to the illy he domeit collection is considered to be one of the largest collections of miniature books he collection is titled  ears of iniature ooks he domeit collection ranges the entire history of human record keeping in miniature form from cuneiform tablets of circa   to contemporary small press and artists books ichael  slan ollectionedit ichael slan producer of the atman movies donated over  comics to the illy ibrary arts of the collection were displayed at the illy ibrary in an exhibition called omics as ultural ythology he ichael  slan ollection of omic ooks and raphic ovels from eptember   to ecember   he slan ollection also contains a vast array of action figures as well as other pop culture memorabilia which can be accessed through the illy ibrary equest ystem he collection is currently housed at the uxiliary ibrary acility  and can be accessed for use through  the ndiana niversity ibrary catalog erry locum echanical uzzle ollectionedit n  the library received a collection of  mechanical puzzles and  puzzle related books from erry locum he collection is the largest assemblage of its kind in the world echanical puzzles are handheld objects that must be manipulated to solve the puzzle he collection spans multiple centuries and five continents he collection is on permanent display in the locum uzzle oom and includes puzzles that can be handled by visitors he illy ibrary is currently developing an online database of the collection rench evolution ocumentsedit he illy contains an extensive collection of material that centers on the rench evolution of  he collection contains approximately seven thousand pieces and includes all types of printed materials such as journals polemical pamphlets and many other documents he collection consists of  legal publications he majority of the other documents are royal and administrative acts published in pamphlet form between  and  here are royal edicts arrets addresses declarations reglements lettres patentes rapports ordonnances memoires lois and various other titled or untitled official documents ernardo endel ollectionedit he endel collection is divided into two categories one relates to the period of geographical discovery and exploration and the other consists of additions made by the illy ibrary after the original acquisition he geographical and discovery collection ranges from the th through the th century t includes the great cosmographic and geographic works of tolemy as well as narratives of the discovery and conquest of the ew orld he emphasis of the subsequent acquisitions is on atin mericana from the th through the th century with particular attention to exican history he endel oom in the illy was dedicated in  ames hitcomb iley ollectionedit he illy ibrary has the personal papers of ames hitcomb iley in a variety of manuscript collections here are also miscellaneous uncataloged materials that includes clippings scrapbooks photographs memorabilia and other ephemera he majority of his writings and personal correspondence are found in the iley mss undreds of ileys books are listed in ndiana niversitys online catalog n the th anniversary of his birth the library produced an online exhibition that explores ames hitcomb ileys impact on merican society and the th century literary world he illy ibrary has other ndiana authors in their collection as well ilm adio  elevision ollectionedit he illy ibrary houses  motion picture scripts including the second draft script for the film hariots of ire n order to supplement the script material the library added to the collection material from rson elles ohn ord lifford dets and most recently eter ogdanovich he library is home to   radio scripts and the papers of ance ieveking and   ridson he television scripts consist mostly of material from ohn creevey as well as scripts from such television shows as tar rek and ission mpossible he ohn ord ollection includes four of his scars  one which is on display  stills from enur  script drafts and correspondence heet usicedit he library houses  pieces of sheet music he prominent collections are the am eincent ollection of merican heet usic the tarr heet usic ollection and the ildermuth ollection of oagy armichael heet usic ale essick ollectionedit he library has the original pen and ink drawings from ale essicks comic renda tarr he collection is organized and searchable by date ystery riters of merica ollectionedit he material is made up of papers relating to meetings correspondence drafts conferences periodicals and books from the ystery riters of merica association lath anuscript ollectionedit he library has  poems as well as  examples of correspondence writing and memorabilia from ylvia lath he poems are arranged in chronological order and the miscellaneous items are organized by type  books from laths only library are housed at the illy ibrary as well
```


**References** - </br>
[1] - https://www.sciencedirect.com/topics/engineering/metropolis-hastings-algorithm

[2] - https://www.stat.purdue.edu/~mdw/CSOI/MarkovLab.html


## Part 3: Spam classification

Let’s consider a straightforward document classification problem: deciding whether or not an e-mail is spam.
We’ll use a bag-of-words model, which means that we’ll represent a document in terms of just an unordered
“bag” of words instead of modeling anything about the grammatical structure of the document. If, for
example, there are 100,000 words in the English language, then a document can be represented as a 100,000-
dimensional vector, where the entries in the vector corresponds to a binary value — 1 if the word appears
in the document and 0 otherwise. Of course, most vectors will be sparse (most entries are zero).
Implement a Naive Bayes classifier for this problem. For a given document D, we’ll need to evaluate
P(S = 1|w1, w2, ..., wn), the posterior probability that a document is spam given the features (words) in
that document. Make the Naive Bayes assumption, which says that for any i 6= j, wi
is independent from
wj given S. (It may be more convenient to evaluate the likelihood (or “odds”) ratio of P (S=1|w1,...wn)
P (S=0|w1,...,wn)
, and
compare that to a threshold to decide if a document is spam or non-spam.)
To help you get started, we’ve provided a dataset in your repo of known spam and known non-spam emails,
split into a training set and a testing set. Your program should accept command line arguments like this:
./spam.py training-directory testing-directory output-file
The training-directory can be assumed to contain two subdirectories called spam and notspam, containing
email files that can be used to estimate the needed probabilities of the model. The testing-directory contains
test emails, one per file; your program should output a output-file in a format like this:
00393.85c9cd10122736d443e69db6fce3ad3f spam
01064.50715ffeb13446500895836b77fcee09 notspam
and so on, where the first part of each line is a filename and the second is predicted class (spam or notspam).

## Solution 
The solution implements the following sections as explained - 

#### Reads the input files - 
- Since the HTML mail files needed considerable amount of cleaning, we chose to only consider the subject of the mails for the bag of words model. Although we did end up cleaning the entire e-mails and run that through the Naive Bayes, we did not see any significant improvement from it.
- All special characters and numerical characters were also eleminated when creating the words/tokens (in the bag of words)

#### Naive Bayes Model - 
- The total number of spam and ham messages were counted and generated corresponding probabilities
- Counted the number of times a word occurred under a spam mail and a non-spam mail and generated corresponding probabilities
- When given a new file for evaluation, the model sums up the log probabilities all the words/tokens in the given mail using the spam probabilities and ham probabilities
- In case of a word that has not been seen before, a "generic" probability is put in place P(A given mail is Spam) or P(A given mail is Ham)
- Finally, we compute P(Spam)/ P(Spam + Ham). If this is greater than 0.5 it is classified as spam, else ham

#### Output - 
- The implementation carries out the above operations on the contents of the test folder and stores the results in the expected format in the user mentioned text file

####  Accuracy - 
We have observed an accuracy 90% on the test dataset provided as part of the assignment.
