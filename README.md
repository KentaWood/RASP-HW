# RASP-HW

**Problem 1:**
Write an s-op that contains the indices in reverse order.

**Solution:**
```
>reverse_indices = length - indices - 1;
> reverse_indices("hello")
[4, 3, 2, 1, 0]
```

**Problem 2:**
Write a function that "rotates" the input text by the specified number of characters.

**Solution:**
```
>def rotate(tokens, n) {
    shifted_indices = (indices - n + length) % length;
    return aggregate(select(indices, shifted_indices, ==), tokens) ;
}
> rotate(tokens, 0)("hello")
"hello"
> rotate(tokens, 1)("hello")
"ohell"
> rotate(tokens, 2)("hello")
"lohel"
```

**Problem 3:**
Write a function that takes a sequence as input and "swaps every letter with its neighbor".
Specifically, for every even index $i$, positions $i$ and $i+1$ will be swapped;
if the length of the sequence is odd, then the last element should not move.

**Solution:**
```
>def swap(seq) {
  return aggregate(select(indices, indices, ==), tokens, "x") if indices == length - 1 and length % 2 == 1 
         else (aggregate(select(indices, indices + 1, ==), tokens, "x") if indices % 2 == 0 
               else aggregate(select(indices, indices - 1, ==), tokens, "x"));
}
> swap(tokens)("hello")
"ehllo"
> swap(tokens)("ababab")
"bababa"
```

**Problem 4:**
Write a function that returns the maximum value in the sequence repeated for every position.

**Solution:**
```
>def maxseq(seq){
	      sorted = sort(tokens, tokens);
	return aggregate(select(indices,  length-1, ==), sorted);
}
> maxseq(tokens)("ababcabab")
"ccccccccc"
```

**Problem 5:**
Write a function that performs sequence reversal "autogeneratively".
That is, it will take a sequence as input, and the sequence will contain a special token `$` that marks the "end of the prompt".
The text before the `$` should be unchanged, and the text after the `$` should be the text before the `$` reversed (this text represents the model's response to the prompt).
The code should be robuse to the case when the length of text after `$` is not the same as the length of text before `$`.

**Solution: (what I Have, did not 100% finish)**
```
>ollar = select_from_first(tokens, "$");

>dollar_p = aggregate(dollar, indices);

>def secondhalf(seq) {
    reverse_selector = select(indices, dollar_p < indices, ==);
    return aggregate(reverse_selector, reverse_func(seq));
}

>def reverse_ag(seq) {
    reversed_indices = length - indices - 1;
    return aggregate(select(reversed_indices, indices, ==), seq);
}
> reverse_ag(tokens)("hello$     ")
"hello$olleh"
> reverse_ag(tokens)("hello$ ")
"hello$o"
> reverse_ag(tokens)("hello$X")
"hello$o"
> reverse_ag(tokens)("hello$XXXXXXXXXX")
"hello$olleh     "
```

**Problem 6:**
Write a function that counts the number of times a certain token appears in the input sequence.

**Solution:**
```
>def howmany(seq, key) {
    return aggregate(select(indices, indices, ==), selector_width(select(seq, key, ==)));
}
> howmany(tokens, "a")("hello")
"00000"
> howmany(tokens, "h")("hello")
"11111"
> howmany(tokens, "l")("hello")
"22222"
```

**Problem 7:**
Write a function that counts the number of times a certain token has appeared in the input sequence so far.
Explanation:
Creates a cumulative count of the occurrences of atom in seq.
Adjusts and rounds the count by multiplying with the current index.

**Solution:**
```
>def howmany(seq, atom) {
    counts = aggregate(select(indices, indices, <=), indicator(seq == atom));
    return round((indices + 1) * counts);
}
> howmany(tokens, "a")("hello")
"00000"
> howmany(tokens, "h")("hello")
"11111"
> howmany(tokens, "e")("hello")
"01111"
> howmany(tokens, "l")("hello")
"00122"
```

**Problem 8:**
Create your own "interesting" problem statement.
Write a function/s-op that solves this problem.
Explanation: marks the last instance of the token 

```
> def highlight_last_instance(seq) {
       return indicator(mark_last_instance(seq, tokens));
   }
>highlight_last_instance(tokens)("hello");
	 =  [1, 1, 0, 1, 1] (ints)
> highlight_last_instance(tokens)("babababa");
	 =  [0, 0, 0, 0, 0, 0, 1, 1] (ints)
```
