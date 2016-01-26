# Char-RNN experiments

These files are experiments in computer-generated text. Just for fun. The experiments use on Andrej Karpathy's [Char-RNN](https://github.com/karpathy/char-rnn) software, an easy-to-install implementation of a character-based recursive neural network. 

How it works, in simple terms: Feed it text (the more the better), let it train on the text for a few hours, and it'll generate sample text that tries to match the original. More info in Andrej's [blog post](http://karpathy.github.io/2015/05/21/rnn-effectiveness/). This isn't a totally new idea (Markov chains can do similar things) but the implementation is new and fun and can be startlingly accrate.

Among other things, RNNs -- and more specifically the Char-RNN software used here -- has generated fake [Shakespeare](http://karpathy.github.io/2015/05/21/rnn-effectiveness/) (scroll halfway down), [speeches by President Obama](https://medium.com/@samim/obama-rnn-machine-generated-political-speeches-c8abd18a2ea0), [baby names](https://plus.google.com/+AndrejKarpathy/posts/GuutNpJKCUp), and episodes of [Friends](https://twitter.com/_Pandy/status/689209034143084547).

My experiments, and some of the results:

## Experiment: Taylor Swift lyrics
I used a web scraper to download all of T-Swift's published lyrics, about 10,000 lines or 300,000 characters total. I concatenated the songs together, like this:

	'Cause that's where you belong
	Until Brad Pitt comes along
	'Til Brad Pitt comes along
	Yeah, oh oh
	Until Brad Pitt comes along

	I stay out too late
	Got nothin' in my brain
	That's what people say, mmh-hmm
	That's what people say, mmh-hmm

Here's how sample generated lyrics looked with Char-RNN's default training settings:

	I hind eed to changhen evermong is hearter
	That me along, you need me to be you, seiting me
	'Cause I know then's it, can't lookon?
	I'll be to ever donne) I can't spy
	But saybed that nave ene my shins forner
	But love on

Decreasing the `seq-length` and `dropout` made it better:

	When I lave about you
	You want there more
	And for the way it was a could love
	Wondering it with you, of a I stond white song porl in tel whole
	Just know what I'm the sutsratter
	I was here heart and not?

But still not amazing. I think it's because the data set is small -- Karpathy recommends a million characters for a good data set, and Taylor's only written about 300K.

## Experiment: English Words

This worked much better, I think partly because single words are easier to fake than complete sentences, or stanzas of songs. Here I used a dictionary file of 109,000 words, one word per line. (No definitions, just the plain words.) Here's a sample from the input file:

	tanking
	cryostat
	cusped
	lineable
	schmaltziest
	burred
	pearled
	pauperism
	therapeutic
 
I shuffled the input file with `gshuf` so that it wouldn't be in alphabetical order (which tricks the trainer into thinking that alphabetical order is important). Here's what the trained model came up with, with default settings:

	worsawing
	reascondantly
	prenigation
	burriness
	extrutes
	semisbutation
	adopteed
	quarrely
	gaines
	boubles
	semifuized
	automatable
	stenons
	fruitly
	subscriptionally
	abasion
	minnistically

Amazing! They're great candidates for [Sniglets](https://en.wikipedia.org/wiki/Sniglet). Remember those? "Words that don't exist in the English language, but should." A future experiment would be to train a model with both words and definitions.

## Experiment: Slang Words

Can a recurrent neural network match the ability of 13-year-olds to come up with words like `bae`, `on fleek` and `dis`? I web scraped 44,000 slang terms (no definitions, just the terms). I shuffled it, like before, and the input file looks like this:

	in the house
	riled up
	made in heaven
	throw down
	hangover
	treeware
	lock down
	randy-scrandy
	facepalm

The result was hilarious. Try these on for size:

	toe rated
	prunk
	wicky-dirty
	go fuzz baked
	jumber the trubs it
	two sarf
	ploper the dilly
	skank-blank
	lose (one's) bag

## Experiment: Slang Words For Good Things

I limited the list to only include words that were for good or positive things, 610 words like these:

	cinchy
	grouse
	cramazing
	slammin'
	awesome
	mack daddy

The output is a little bit more positive (maybe?) than the larger slang set, but lower-quality because it's a smaller number of words being input:

	meckengy
	rlonk rink fingy
	bicky
	cookhey
	bagsy
	cpap
	biss
	peppey

## Experiment: Slang Terms for Sex

Some of the slang terms above already sound like sex acts, so I limited the input to 443 words related to [kissing, sex, and not-quite sex](http://onlineslangdictionary.com/thesaurus/words+meaning+kissing,+sex+and+not-quite-sex.html) (link is somewhat NSFW). The input file looks like this:

	kissy face
	get lucky
	seal the deal

(I'm only showing polite words; plenty of naughtier terms are in the data set.)

The results were pretty great, but not as good as general slang, probably because the input set was so small:

	frenchank
	jump (one's) base
	bast off
	mot the beast
	chake the munherg
	frekin wow

*Frekin wow!*

## Experiment: The RockYou Password Leak

In a totally different direction, I trained the system on the 14,344,391 leaked unencrypted passwords from the [RockYou](https://wiki.skullsecurity.org/Passwords) list. (This list is valued by security specialists because it's a faithful rendering of passwords actually being used in the wild.) The input file (shuffled, again) had passwords like this:

	allcabelos41
	8eb51d
	minhwan
	moncharee
	pinkroses21
	veronica130280
	porku64

The output produced similar looking passwords, like these:

	edgarthluvsimiss
	SKIMOSS
	143yangel
	dorichac
	TILLYPRESIOSAS10106
	123nah
	emight1473h
	diego21074

I cleaned these against the original, removing unintentional duplicates, then checked them against a similar but bigger password dump of 50,284,014 passwords, hoping to find that the RNN-generated passwords were actually real!

It didn't really work. Only 3% of the generated passwords were found in the larger list.


------------

## Try it yourself

This repo contains training files that you can use to play, if you like. I haven't included the original datasets here, because (in some cases) it's quite large and (in other cases) it's bound by copyright and probably not cool of me to republish. 

Want to try this yourself? Some knowledge of the command line is required. Follow the instructions to install [Char-RNN](https://github.com/karpathy/char-rnn), then feel free to point `th train.lua` to the folders in this repo, which have vocabularies ready to go.

## Commands

Helpful commands as notes for myself. Your system and usage may vary.

To train a model (using OpenCL on my Macbook):
 
	th train.lua -data_dir path/to/datafiles -checkpoint_dir path/to/datafiles -opencl 1 -seq_length 20 -dropout 0.8

The `seq_length` and `dropout` parameters should be smaller for smaller datasets (under a megabyte). These settings are a little black-magic but the Char-RNN repo does give some advice on the subject.

To show samples from a trained model

	th sample.lua path/to/model.t7 -opencl 1 -verbose 0 > output.txt

(Add the `-temperature` flag and a number between 0 and 1 to vary the daringness of the sampler. 1 is safe and 0 is daring.)

To remove duplicates from an output file (compared to the input):

	awk 'NR==FNR{a[$0];next} !($0 in a)' input.txt output.txt > cleaned.txt

To find the lines duplicated in two files:

	awk 'NR==FNR{arr[$0];next} $0 in arr' input.txt output.txt > dupes.txt

To count the number of lines in a file:

	wc -l 
