# Bits and Bytes

---

**A Little Information Theory**

Let's start out with some theory.

In the late 1940s, research into digital communication led to a scientist and mathematician by the name of *Claude Shannon* creating an entirely new field of mathematics; *Information Theory*. *Information Theory* revolves mostly around transmitting information through "channels", often in the presence of noise (leading to plenty of work related to error-correcting codes), and data compression.

One of the core concepts in *Information Theory* is *Entropy*, or the total amount of information required to represent something. In this case, information is measured by the number of symbols required to communicate a message, while *Entropy* is a measure of the minimum number of symbols required to communicate that same message.

```
Entropy = log(# of possible states)

The base of the logarithm is equal to the number of different symbols that can be used in communication.
```

The unit for entropy depends on the base of the logarithm, but *bits* or *binary digits* are the most common unit (which assume log base 2).

Okay, this is pretty abstract. Perhaps an example may be helpful.

Suppose we want to send a message to someone regarding the weather, and we want to do so as efficiently as possible in terms of *Entropy*. We decide we want to differentiate between 3 different types of weather; ```Clear```, ```Cloudy```, and ```Rainy```.

Because at any point the weather will only be one of these 3 states, we can say that we have 3 different states we wish to communicate.

Now we also need to consider how we're able to communicate the message. If we only have 2 possible symbols available; a 1 or a 0 perhaps, then the entropy is ```log2(3)```, or 1.585 bits. This means that, provided we have two possible symbols, each message must be at least 1.585 symbols long. Because we can't have partial symbols, we must round this up to 2 bits per symbol. We can easily make a list of 2-bit messages, and associate 3 of them with these states.

```
00 - Clear
01 - Cloudy
10 - Rainy
11 - *Unusued*
```

Provided with more symbols, we can shorten the messages. If we have 3 possible symbols (say 0, 1, and 2), we can send shorter messages.  In fact, we can now shorten each of these messages to just 1 symbol. If we decide to represent numbers rather than weather messages, the maximum number of numbers we can represent is the number of different symbols we allow ourselves raised to the power of the number of symbols per message. If we use 10 symbols (0 through 9), we can represent numbers in more or less the same way we already write them.

This may seem like a great idea; increase the number of possible symbols. A great number of people, when hearing that computers use "just ones and zeroes" often assume increasing that to say, 3 symbols (0, 1, and 2); switching from binary to tertiary has clear benefits, right? In practice, more than 2 symbols require analog signals, and the more different signal strengths that have to be differentiated between, the harder it gets to differentiate, and also the more susceptible the system is to errors creeping in. Plus, power consumption scales linearly with the number of symbols, while actual information content scales only logarithmically. More states can also make hardware more complex.

As a result, binary is the most error-resistant, most power efficient, and least complex solution.

---
