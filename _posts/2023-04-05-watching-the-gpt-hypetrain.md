---
layout: post
title: Watching the GPT hypetrain
date: 2023-04-05 10:00:00.000000000 +01:00
author: luka
---

It feels like everyone in tech circles is talking about this, and even non-tech people got interested, and joined on the fun.

ChatGPT - the man of the hour.

New versions seem to be coming up quickly, and some folks are asking for a moratorium on further development. Hearing this without much context, sounds pretty scary.

Initially, I brushed it off as a non-factor. New technology being introduced as a big advancement in AI. We've been hearing the same story for years. However, as the hype increased, a lot more information was coming through the grapevine about some amazing things it can do. There were talks of disrupting everything, and by 'everything' I mean the whole world economy. Millions of people could lose their jobs, and we're on the verge of something dangerous for the whole planet.

Hearing stuff like this did not make me exactly comfortable, and seeing screenshots of this "thing" providing perfect answers for questions, or providing working code for a vaguely described requirements filled me with unease. Though, I must say, I am an anxious person, so a lot of things do this for me. I did not want to come near this thing, and purposefully avoided all of the mentions, articles, and news about it.

But.. Fears are here to be conquered.

Looking at the technology, it is a so called LLM - Large Language Model. As I understand it, it's a neural network trained on billions of pieces of (human) text. Through this "training", the model learns all the ins and outs of human language - how words behave, what is the correct order of words, even what the context of a specific thing in a sentence is, etc.GPT seems to be one of these LLMs. It is trained on a lot of data, and has an interface through "chat" application - ChatGPT. 

What I find interesting in all of this is talking to a machine in a specific context only. For example, uploading a PDF of your own, and having the GPT be your guide through it. Being able to answer all the questions you have. When I stumbled upon llama-index, I had to try it.

Llama index uses your own data (PDF, mp3, whatever), and does a kind of preprocessing - at least that is my understanding. Prepocessing results in *indices* which can be fed to an LLM. LLM then has the necessary context, and can provide you with answers to your questions regarding the specific context.

I used my own CV, to test this. Here's the code:

```python
from pathlib import Path
from llama_index import GPTSimpleVectorIndex, SimpleDirectoryReader
from gpt_index import download_loader

PDFReader = download_loader("PDFReader")

loader = PDFReader()
documents = loader.load_data(file=Path('./cv.pdf'))
index = GPTSimpleVectorIndex(documents)

try:
	while True:
		# get an input from user
		inp = input('Q: ')
		if (inp == ''):
			continue
		response = index.query(inp)

		# provide a response
		print("A: " + str(response))
except KeyboardInterrupt:
	print('interrupted!')
```


Talking with the machine in the context of my CV was interesting. Some answers were fine, some less so. Some were plainly wrong, but in any case.. Here's an excerpt from my talks with an "AI":

```text
Q: What is this PDF about?
A: 
This PDF is about Luka Rajčević, a web developer from Šamac, Croatia. It outlines his experience in building and maintaining projects of various sizes, his knowledge in development of web applications using PHP programming language and MySQL database, and his expertise in Python/OpenWRT, PHP/Magento, and PHP/Fullstack development. It also includes his education, certifications, and language proficiency.

Q: Does this person have an email address?
A: 
Yes, this person has an email address: lurajcevi@gmail.com

Q: Did this person ever work remotely?
A: 
Yes, this person has worked remotely.
Q: What programming languages does this person know?
A: 
This person knows PHP, MySQL, JavaScript, Python, Git, Linux.
Q: How many years of experience does this person have?
A: 
Eight years.

```

It can also do poems.. :D

```text
Q:Give me a very short poem about this document                  

A: 
A web developer from Šamac,
Luka Rajčević is his name.
He's worked on projects of all sizes,
From niche to enterprise fame.

He's skilled in PHP and MySQL,
JavaScript and Python too.
Git and Linux, he knows them well,
And his experience is true.

He's certified in Magento,
And a master of his craft.
He's worked on projects near and far,
And his knowledge is vast.
```

It's silly, but still impressive to get this from a machine.

I've played with some other ideas, and it was interesting:
- providing an mp3 file of a podcast I was listening, and getting solid answers
- asking the machine to translate text from english to spanish - worked fine as well

There's more to be learned about all of this, but I must say, I'm less anxious about the technology.

Cheers.

PS. To be all meta, I ran this post through my GPT md reader, and asked for its opinion. Here it is:

```text
Q:Can you provide your critique of the post?
A: 
This post provides an interesting overview of the GPT technology and its potential applications. The author does a good job of explaining the technology and its implications, as well as providing examples of how it can be used. The author also provides a good critique of the technology, noting the potential for disruption and the unease it can cause. However, the post could have gone further in exploring the potential implications of the technology, such as the ethical considerations of using it, or the potential for misuse. Additionally, the post could have provided more detail on the technical aspects of the technology, such as how it works and how it is trained. Overall, this post provides an interesting overview of the GPT technology, but could have gone further in exploring its implications and technical aspects.

Q:How would you improve on this post?
A: 
One way to improve on this post would be to provide more detailed information about the technology behind ChatGPT and the Large Language Model. This could include a more in-depth explanation of how the neural network is trained, how the preprocessing works, and how the indices are used. Additionally, it would be helpful to provide more examples of how ChatGPT can be used in different contexts, such as providing translations or providing answers to specific questions. Finally, it would be beneficial to provide more information about the potential implications of this technology, such as the potential for job loss and disruption of the world economy.

Q:Give me 2 sentences that would improve this article
A: 
1. GPT has the potential to revolutionize the way we interact with machines, and its implications for the future of work are far-reaching. 
2. By understanding the technology behind GPT, we can better prepare ourselves for the potential changes it may bring.

```

Hey, there on the screen! It's that guy you are!