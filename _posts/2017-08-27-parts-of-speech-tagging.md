---
layout: post
title:	"An improvement on the Michael Collin's part of speech tagger"
date:	2017-08-27
categories: blogging
---
Parts of speech tagging. You might be used to it from elementary school. The process of assigning labels such as 'noun', 'adjective', 'preposition', and so on is an important part in natural language processing. You might use your traditional labels to label the sentence below as such:

| pronoun | verb | preposition | article | noun | preposition | noun |
| I | went | to | the | market | for | John |
{: .bordered-table .centered}

For language processing, we generally use a more specific set of tags. One such set is the Penn TreeBank Tagset. If we were to tag the above sentence using these tags, we would end up with something like:

| PRP | VBD | TO | DT | NN | IN | NNP |
| I | went | to | the | market | for | John |
{: .bordered-table .centered}

These tags probably don't mean much if this is your first time seeing them. Below I've provided a quick description of the 36 different tags in this set. It should be noted that this isn't the only tag-set used in natural language processing, but it is one of the more common ones.

| Tag | Name | Description | Examples
| CC | Coordinating Conjunction | [Conjunction junction, what's your function, hooking up words and phrases and clauses](https://www.youtube.com/watch?v=4AyjKgz9tKg)| I got milk **and** honey \| Eat it now **or** later |
| CD | Cardinal Number | Counting numbers | There are **two** dogs \| I have **one million** dollars |
| DT | Determiner | Goes before a noun to tell us which or how many | I went to **the** circus and saw **an** elephant \| I tried **each** flavour |
| EX | Existential there | There when it indicates the existence or non-existence of something | **There** is a house in New Orleans \| When is **there** an excuse to do that? |
| FW | Foreign word | Just a foreign word | Foreign words appear **ad infinitum** (**e.g. i.e.** and **etc.**) |
| IN | Preposition or subordinating conjunction | Prepositions indicate location (e.g. in, beside, on) | The car **from** my house came **in** for a tune-up |
| JJ | Adjective | Words that describe nouns | [It was a **flying** **purple** people-eater](https://www.youtube.com/watch?v=Rx47qrH1GRs) |
| JJR | Adjective comparative | An adjective that works comparatively | As he walked, the water got **deeper** \| He never pursued **higher** education |
| JJS | Adjective superlative | Like comparative adjective, but implies an extreme | The **shortest** route is that one \| He's the **fastest** player on the team |
| LS | List item marker | Words denoting the beginning of a list item | **First**, turn left on Berry Dr. \| **One** build a business. **Two** Question mark. **Three** profit. |
| MD | Modal | Modals augment verbs to show tense or intent | We **could** walk there \| I **should** eat \| I **can** go if you want |
| NN | Noun, singular or mass | People, places, and things | I have a **dog** \| Come **home** \| You are lost in a **crowd** |
| NNS | Noun, plural | Multiple people, places, and things | I have **dogs** \| Where are my **friends**? |
| NNP | Proper noun, singular | Named people, places, or things | I went to **Alaska** \| I own a **Toyota** \| There's my friend **John** |
| NNPS | Proper noun, plural | Multiple named people, places, and things | We drove to the **Alps** |
| PDT | predeterminer | A word augmenting a determiner | I ate **half** a plate of spaghetti \| It was **quite** a party |
| POS | possessive ending | A possessive ending appended to a noun | That was Fred**'s** mistake \| The castle**'s** walls weren't strong enough |
| PRP | personal pronoun | A pronoun referring to a noun | **I** wear short shorts \| **They** love chocolate |
| PRP$ | possessive pronoun | A pronoun indicating something that is owned | **Our** friends are here \| **Their** house is magnificent |
| RB | Adverb | A word modifying a verb | I ran **fast** \| I fell **slow** |
| RBR | Adverb, comparative | An adverb that works comparitively | I ran **faster** than him \| I fell **slower** than a feather |
| RBS | Adverb, superlative | Oddly these in real-life examples usually augment an adjective | It was the **most** famous wonder of the world |
| RP | Particle | Particles don't really fit into normal parts of speech | He stood **up** to the challenge of his rivals |
| SYM | Symbol | Symbols not in the normal alphabet. I guess. Anyone have a corpus with a SYM? | |
| TO | to | Just the word to | I went **to** the market |
| UH | Interjection | Exclamatories and filler words that don't affect the sentence | **Uh**, that is a difficult question \| **CHRIST,** that's a big fish |
| VB | Verb, base form | The general base form of a verb that you'll find in a dictionary. Basically it's the infinitive minus the leading 'to' | I will **fall** \| He can **jump**  |
| VBD | Verb, past tense | The form of a verb when it is being done in the past | I **told** you \| I **said** it would happen |
| VBG | Verb, gerund or present participle | The 'ing' form of a verb | I am **jumping** for joy \| It is **spinning** |
| VBN | Verb, past participle | The 'ed' form of a verb | Jack **jumped** over the candle stick \| It **fell**(irregular) |
| VBP | Verb, non-3rd person singular present | Any present tense verb that isn't in 3rd person singular (he, she, it)  | That's where I **live** \| These people **are** few |
| VBZ | Verb, 3rd person singular present | Any present tense verb that is in 3rd person singular | He **is** fun \| He **runs** a lot |
| WDT | Wh-determiner | A determiner starting with 'wh' | **What** is apparent is the lack of planning \| **Which** was the right one |
| WP | Wh-pronoun | A pronoun starting with 'wh' | **What** was surprising about John was his lack of planning \| For **whom** the bells toll |
| WP$ | Possessive wh-pronoun | A 'wh' pronoun denoting ownership | **whose** is this? |
| WRB | Wh-adverb | An adverb starting with 'wh' or another question word | Which is **why** I'm sitting here \| Seeing **how** quickly he ran |
{: .bordered-table}

A quick linguistic note. These tags pretty much only work for English. Especially given the 'wh' word categories. For our Spanish speakers, the modal 'will' disappears when 'I will eat' becomes 'Comere'. English also has very limited verb forms (note how only third person singular is the only unique form in the present tense). So remember that your tag set can change vastly given the language you're using. But, let's get back to the Penn Treebank Tagset.

You can imagine that it would take a lot longer to tag a document with set of 36 tags compared to the six or so education tags we mess with in grade school. This increase in tags leaves  a large amount of room for error from the simple number of choices. Then take into account that documents being tagged are larger than a simple 5 word sentence (I'm looking at a 29310-word tagged document of British news currently). That translates to a lot of hours of interns and lab assistants writing these tags over and over. Even more insane, they aren't even limited to these 36 tags, as ambiguous sentences like ["The Duchess was entertaining last night"](http://www.comp.leeds.ac.uk/amalgam/tagsets/upenn.html) lead to the word entertaining being tagged as possibly a verb, VBG, or an adjective JJ, which is then represented as 'VBG\|JJ'. This means there's a possible 666 tags including just the single tags and tag pairings(36 choose 2 plus 36). If you want to include all ambiguous tags, we end up with 68,719,476,735 possible tags. It would be an impressive feat though to find a sentence with the ambiguity for 5 tags, let alone 36. Generally, automated taggers will just go with the best guessed single tag. Then it should be noted that word tagging is just the first step in making a proper treebank, which should also label [clauses and phrases](http://web.mit.edu/6.863/www/PennTreebankTags.html).

## Michael Collin's Viterbi tagger

For the purpose of this blog post, we're just going to look at word labeling, and my groups' progress with the Michael Collin's implementation of the Viterbi tagger. As the actual code used in this project was a modification of a homework assignment, I will not be releasing the code :(. I will however release the [modified Aspell dictionary with Penn Treebank POS tags](https://github.com/moore3071/dictionary-penn-treebank), and link to the [annotated TedTalks](http://ahclab.naist.jp/resource/tedtreebank/) and [British news Corpus](http://nclt.computing.dcu.ie/~jfoster/resources/bnc1000.html). Our project used this modified dictionary and our training corpus to limit the tagging to tags that had already occurred for the words. There's little reason to assume that 'the' is going to ever be tagged as TO or SYM. We expected this would speed up the tagging of a document without sacrificing accuracy.

So, how does the Michael Collin's tag implementation work? I could try to explain it myself, but honestly the best explanation is in [Michael Collin's paper](http://www.cs.columbia.edu/~mcollins/courses/nlp2011/notes/hmms.pdf). Don't be scared by the page count since it has LaTeX's default generous margins and is half figures/equations. I'll give you a few minutes now to read through Collin's paper.


Done?

For a quick and generalized explanation (of supervised learning), we have two sets of prelabeled data. One of these will be our learning set, and the other will be our test data. We teach our program with the learning set to find a model to assign tags given new inputs. Given the learning set, our model should have an accurate function to label some data it hasn't seen before (our test data). We then ask it to label the test data without looking at the real labels, and then look at the labels and evaluate how accurate it was. In order for the model to be accurate, the learning set has to be sufficiently large. Just imagine trying to guess what comes next from the series `2,4`. Is the next number 6(+2)? Or 8(\*2)? Or even 16(squared)? Now imagine how much data is needed to guess the next part of speech tag given the previous two tags and what the next word in the test data was tagged in the training data.

What Collin's paper doesn't discuss is the massive time-cost of this tagging system. In this homework assignment, it wasn't uncommon to find students taking 6 hours to tag a document. This is because each word has to be run through every possible tag to find out which option is the best, for tens of thousands of words. The tags in the document tended to be around 85% accurate though once done. So, how do we speed this up? Let's not check every one of the 36 tags. We can easily make a dictionary covering the majority of words and their possible tags(and by make a mean steal from Aspell). Given this dictionary and a training corpus from a document on the same topic, we can easily restrict most of our words to around 4 tags compared to the 36 we're used to. Meanwhile the unknown words get tested against all 36 tags.

The experimental method and the original method were ran with 1, 5, and 10 iterations of learning. The runtime improvements came in between 600% and 850%. The accuracy also surprisingly improved with the experimental method between 2% and 8%. 
