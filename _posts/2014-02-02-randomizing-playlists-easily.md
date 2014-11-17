---
layout: post
name: Randomizing Playlists
categories: [python]
tags: [python, music, playlist, random, easy]
class: python
---

##The Reference

([found here](http://ssodelta.wordpress.com/2014/01/31/how-to-shuffle-a-playlist-correctly/))

> A possible algorithm on a playlist of n songs might be:  
  
> 1. Find random k such that gcd(k,n) = 1.
2. Set i = k.
3. Play song Si.
4. Set i = i + k (mod n)
5. If i != k, go to step 3.
6. Go to step 1.

While it's certainly a viable approach that can consistently be implemented in any language, it overcomplicates the initial problem.

##A Simpler Approach

When someone wants their playlist to be randomized, most people want their items to keep playing continuously but without repeating any songs. We can start off with a very simple approach of plopping all our playlist items into an array and shuffling that array. When the end of the playlist is reached, reshuffle the array.

However we quickly run into the same problem: what if the last item becomes the first or the second position of the new array? How can we appropriately handle that while still keeping this concept of a simple approach?

Easy, we pick an arbitrary number of songs that we believe is "far" enough a split, so that the person is happy that songs are not repeating. We can pick a percentage, such as 50% and always split the array in half then shuffle and alternate the halves. This will work for any size array, the small and the big ones.

Assuming we have the array S of playlist items and we are starting the playlist anew:

1. Set i = n >> 1 where n is the length of S
2. Shuffle array S0...Si and array Si+1...Sn-1
3. Join the arrays if they were split

The bit shift is simply a division by 2 and dropping the remainder all in one step.

Most languages have their own version of an array shuffle that is optimized for that language. It's very wise to acknowledge and use these implementations for performance reasons. Here's an example in Python using the builtin random module.

    import random

	def randomize(playlist):
		index = len(playlist) >> 1

		left = playlist[:index]
		right = playlist[index:]

		random.shuffle(left)
		random.shuffle(right)

		return left + right

	if __name__ == '__main__':
		playlist = ["Shot Me Down", "Crackin", "Gecko", "Raveology", "Rockin", "Ruby", "Black Smoke", "Colors", "We Like To Party", "I Got U", "Alright 2014", "Lion", "Waves", "Do It"]
		
		print playlist
		print randomize(playlist)