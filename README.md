# Parts of speech tagging
A hidden markov model for part of speech tagging

The idea for Hidden Markov Models is the following: Let's say that a way of tagging the sentence "Jane will spot Will" is noun-modal-verb-noun and we'll calculate a probability associated with this tagging. So we need two things. First of all, how likely is it that a noun is followed by a modal and a modal by a verb and a verb by a noun.

Noun ---> Modal ---> Verb ---> Noun

These need to be high in order for this tagging to be likely. These are called the transition probabilities.

Now, the second set of probabilities we need to calculate are these: What is the probability that a noun will be the word Jane and that a modal will be the word will, etc. This also need to be relatively high for our tagging to be likely. These are called the Emission Probabilities.

Noun ---> Modal ---> Verb ---> Noun<br>
&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|<br>
&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|<br>
&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|<br>
Jane&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;will&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;spot&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Will


So, here are sentences with their corresponding tags (N=Noun, V=Verb, M=Modal) 

Mary(N) Jane(N) can(M) see(V) Will(N)

Spot(N) will(M) see(V) Mary(N)

Will(M) Jane(N) spot(V) Mary(N)?

Mary(N) will(M) pat(V) Spot(N)

and we're going to calculate the Emission Probabilities, that is the probability that if a word is, say, a noun that that word will be Mary or Jane, etc. 

So, to do this we do a counting table like this one where, 

<table>
 <tr><td></td><td>Noun</td><td>Modal</td><td>Verb</td></tr>
 <tr><td>Mary</td><td>4</td><td>0</td><td>0</td></tr>
 <tr><td>Jane</td><td>2</td><td>0</td><td>0</td></tr>
 <tr><td>Will</td><td>1</td><td>3</td><td>0</td></tr>
 <tr><td>Spot</td><td>2</td><td>0</td><td>1</td></tr>
 <tr><td>Can</td><td>0</td><td>1</td><td>0</td></tr>
 <tr><td>See</td><td>0</td><td>0</td><td>2</td></tr>
 <tr><td>Pat</td><td>0</td><td>0</td><td>1</td></tr>
<table> 

for example, the entry on the Mary row and the noun column is four because Mary appears four times as a noun.

Now, in order to find the probabilities, we divide each column by the sum of the entries and we obtain the following numbers. 

<table>
 <tr><td></td><td>Noun</td><td>Modal</td><td>Verb</td></tr>
 <tr><td>Mary</td><td>4/9</td><td>0</td><td>0</td></tr>
 <tr><td>Jane</td><td>2/9</td><td>0</td><td>0</td></tr>
 <tr><td>Will</td><td>1/9</td><td>3/4</td><td>0</td></tr>
 <tr><td>Spot</td><td>2/9</td><td>0</td><td>1/4</td></tr>
 <tr><td>Can</td><td>0</td><td>1/4</td><td>0</td></tr>
 <tr><td>See</td><td>0</td><td>0</td><td>2/4</td></tr>
 <tr><td>Pat</td><td>0</td><td>0</td><td>1/4</td></tr>
<table>
 
 And here's another representation of this table.
 
 Noun<br>
 Mary(4/9)   Jane(2/9)   Will(1/9)   Spot(2/9)
 
 Modal<br> 
 Will(3/4)   Can(1/4)
 
 Verb<br>
 Spot(1/4)   See(2/4)   Pat(1/4)

Say, if we know that a word is a noun, the probabilities of it being Mary is four over nine, Jane is two over nine, Will is one over nine, and Spot is two over nine. The other words are zero. Same thing for modal, and for verb. And notice that words can appear repeatedly here like Will which appears as a noun and also as a modal.

That is no problem. So, now let's calculate the transition probabilities. These are the probabilities that are part of speech follows another part of speech. First, in order to actually get the whole picture, we'll add starting and ending tags on each sentence and we'll treat these tags as parts of speech as well.

&lt;S&gt;Mary(N) Jane(N) can(M) see(V) Will(N)&lt;E&gt;

&lt;S&gt;Spot(N) will(M) see(V) Mary(N)&lt;S&gt;

&lt;S&gt;Will(M) Jane(N) spot(V) Mary(N)?&lt;S&gt;

&lt;S&gt;Mary(N) will(M) pat(V) Spot(N)&lt;S&gt;

And now we make a table of counts in this table. We count the number of appearances of each pair of parts of speech. 

Transition Probabilities
<table>
 <tr><td></td><td>N</td><td>M</td><td>V</td><td>&lt;E&gt;</td></tr>
 <tr><td>&lt;S&gt;</td><td>3</td><td>1</td><td>0</td><td>0</td></tr>
 <tr><td>N</td><td>1</td><td>3</td><td>1</td><td>4</td></tr>
 <tr><td>M</td><td>1</td><td>0</td><td>3</td><td>0</td></tr>
 <tr><td>V</td><td>4</td><td>0</td><td>0</td><td>0</td></tr>
<table>

For example, the 3 here in the noun row and the modal column corresponds to the 3 occurrences of a noun followed by a modal. 

&lt;S&gt;Mary(N) Jane(N) can(M) see(V) Will(N)&lt;E&gt;

&lt;S&gt;Spot(N) will(M) see(V) Mary(N)&lt;S&gt;

&lt;S&gt;Mary(N) will(M) pat(V) Spot(N)&lt;S&gt;

Now, to find the probabilities, we divide each row by the sum of the entries in the row. 

<table>
 <tr><td></td><td>N</td><td>M</td><td>V</td><td>&lt;E&gt;</td></tr>
 <tr><td>&lt;S&gt;</td><td>3/4</td><td>1/4</td><td>0</td><td>0</td></tr>
 <tr><td>N</td><td>1/9</td><td>3/9</td><td>1/9</td><td>4/9</td></tr>
 <tr><td>M</td><td>1/4</td><td>0</td><td>3/4</td><td>0</td></tr>
 <tr><td>V</td><td>4/4</td><td>0</td><td>0</td><td>0</td></tr>
<table>

In this way, if we look at, say, the modal row, the probability that the next part of speech is a noun is 1/4, but it's a verb it's 3/4 and that it's a modal or the end of the sentence is 0.

We have our words, which are observations. These are called the observations because they are the things we observe when we read the sentences. And the parts of speech are called the Hidden States, since they are the ones we don't know and we have to infer based on the words. And among the Hidden States we have the transmission probabilities, and between the Hidden States and the observation we have the Emission Probabilities. And that's it. That's a Hidden Markov Model.

