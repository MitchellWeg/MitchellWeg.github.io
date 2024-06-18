---
title: "Pumping Lemma and Ogden's Lemma"
date: "2024-06-18"
tags: ["automatas", "formal-languages"]
---

### The Pumping Lemma
Formally, the pumping lemma states:

Suppose L is a regular language. There exists a p (the pumping length)
such that every w in L can be divided into three substrings, satisfying the following conditions:

$$
w = xyz
$$

$$
|xy| \leq p 
$$

$$
|y| \geq 1
$$

The above says the length (denoted by the pipe symbol '|') of the substring xy should be less than or equal to the pumping length, and the length of the substring y should be greater than or equal to 1.

According to the pumping lemma, if language is regular, we could repeat a certain part in a string (called *pumping*), and the resulting string would still be in the language.

The pumping lemma is used to prove a language is not regular. In order to prove this, we are going to use a *proof by contradiction*. 

If you want to prove a language *is* regular, you can construct an automaton for it.

### Proof
Suppose we have a language:

$$
L = \lbrace a^n b^n \mid n > 0 \rbrace
$$

We want to prove that this language is not regular.

Firstly, let's define in natural languages what the languages states:
There are just as many a's as b's in this language.

But there is a fundamental flaw with this lanugage.
If we want to build an automaton for this lanugage,
it needs to *remember* how many a's came before the b's.
With it's limited memory, this is not possible. We can use the pumping lemma to prove this.

For the pumping length, lets use n:

$$
L = \lbrace a^p b^p \mid p > 0 \rbrace
$$

Lets choose a string, and call it s:

$$
s = w \in L
$$

and:

$$
|s| = p
$$

The pumping lemma states that we can subdivide this string into three parts:

$$
s = xyz
$$

The pumping lemma also states that the length of y is greater than or equal to 1, and the length of xy is less than or equal to *p*.

That means that the string xy only consists of the first p characters, which, according to our language definition, is the first *p* a's.

Because we subdivided s into xyz, x y and z can be any number of a's. The variable *x* is the first part, *y* is the middle part which we are going to 'pump', and z conists of the trailing a's.

Lets change our definition a bit to include the number of occurence of a's:

$$
s = x^iy^jz^{p-i-j}b^p
$$

*Notice we have p-i-j for z, this is because z contains the trailing amount of a's, which is the pumping length p (the complete length of our string) minus the first amount of a's, which is i, and the second amount of occurences, which is j.*

We can now pump the string y:

$$
xy^2z
$$

Which becomes:

$$
s = x^iy^jy^jz^{p-i-j}b^p
$$


Which, when we simplify, would become:

$$
s = a^{p+j}b^p
$$

Which is in a form that is clearly not in our language.