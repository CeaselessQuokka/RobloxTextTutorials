## Introduction
This tutorial is for anyone looking for Lua's regular expression equivalent. You need a basic knowledge of the Lua syntax and zero knowledge of the Roblox API. This tutorial will be relatively lengthy, but I wanted to cover everything I could think of to further your knowledge on string patterns. If something I said was wrong please let me know and I will fix it as soon as I see it.

### What are string patterns?
String patterns is Lua's equivalent to a regular expression (although not exactly like regular expressions). If you do not know what a regular expression is then putting it another way, string patterns are a powerful way to search for content in a string. String patterns are used in conjunction with certain Lua functions to find this content in a string. Some functions that use string patterns are **match,** **find,** **gmatch,** and **gsub.** I will go over these specific functions later in their rightful sections.

### Patterns
There are a list of patterns that have a specific meaning, these patterns are:

- . - any character
- %a - any upper case or lower case letter (A-Z, a-z)
- %c - control characters (refer to control characters section)
- %d - digits (0-9)
- %l - lower case characters (a-z)
- %p - punctuation characters
- %s - space characters
- %u - upper case characters (A-Z)
- %w - any alphanumeric character (0-9, A-Z, a-z)
- %x - hexadecimal digits (0-9, A-F, a-f)
- %z - matches the null terminator (byte 0; \0)

Now let's use some of these in an example. For this example we are going to be use the **match** function. The match function takes a string pattern (or literals, more on this later), and returns the match(es) if they exist. For this scenario, let's say we have a TextLabel with the text: "Price: $400" and you want to get the numbers from the text (the 400 in this case).

```lua
--[[
	First let's initialize our variable just to test,
	this will make it easier to change and test later.
--]]

local TextLabelContent = "Price: $400"

--[[
	Now, let's create the actual pattern that will get the numbers.
	Let's reference our pattern cheat-cheat above. It says that
	"%d" matches any digit. Let's try this. So, if each %d
	matches exactly one digit and there are three digits, 400.
	We must need 3 %d's to match the full number.
--]]

local Pattern = "%d%d%d" -- Match three numbers ANYWHERE in the string

--[[
	Let's use the match function to get the numbers from the string.
--]]

print(TextLabelContent:match(Pattern))
-- We get 400 printed in the output!

-- NOTE: even though we are matching numbers the value returned is a string.
```

Here's the full code without the comments:

```lua
local TextLabelContent = "Price: $400"
local Pattern = "%d%d%d"
print(TextLabelContent:match(Pattern))

-- You could also do this all in one line:

print(("Price: $400"):match("%d%d%d"))
print(string.match("Price: $400", "%d%d%d"))
```

However, there are a couple of problems with this. First, recall when I said the three numbers are matched ANYWHERE in the string. This will only work if we can guarantee the text is always "Price: $" followed THREE numbers. Since the pattern is *%d%d%d* it will only match three numbers. What happens if the price is more than three numbers? Well, you only get the first three digits the match function finds (and it has to be AT LEAST three digits or you will get nil), for example:

```lua
print(("Price: $60"):match("%d%d%d"))
--> nil

print(("Price: $837"):match("%d%d%d"))
--> 837 (like you'd expect)

print(("Price: $9001"):match("%d%d%d"))
--> 900
```

Why does the first match return nil? This because the match is looking strictly for three digits, and there are only two digits, a 6 and a 0. The second example is fine and is what we expect the match to return. The third example matches only the first three digits because we only specified it to find there digits. Of course, you can just add more %d's to the pattern, but this doesn't ensure we get every number, and what if the number is less than the amount of %d's you put? You will get nil. This is where **magic characters** come in handy.

### Magic Characters
Magic characters are other characters in string patterns that provide even more functionality. These magic characters include:

- () - capture
- [] - set
- . - matches any character
- % - escape character
- \+ - matches 1 or more of the specified pattern or literal (greedy match)
- \* - matches 0 or more specified pattern or literal (greedy match)
- \- - also does what * does, but matches the shortest sequence (non-greedy match)
- ? - matches 0 or 1 of the specified pattern or literal

.: Matches any character, example:

```lua
-- One . is equal to one character
print(("Age: 2"):match("."))
--> A

-- Four .'s is equal to four characters
print(("Age: 2"):match("...."))
--> Age:

-- .+ matches at least one character (and anymore after that)
print(("Age: 2"):match(".+"))
--> Age: 2
```

%: Escape character, example:

```lua
--[[
	Let's say I watch to match the percentage in a string,
	I could achieve this by using %d+ to match the numbers
	and a % sign, right? Well, not exactly,
	since the % sign is a magic character it will error
	unless escaped. To escape a magic character you need
	to use the % sign. A bit confusing, but here are a couple examples.
--]]

local String = "This test is 20% of your grade."
print(String:match("%d+%"))
--> this will error

local String = "This test is 20% of your grade."
print(String:match("%d+%%"))
--> 20%

--[[
	The % escaped the other % sign and made it a literal.
	Literals are just that, their literal character.
	For example, a, b, and c are literal characters.
	. however is NOT a literal character because it is a magic character.
	If I were to escape ., then I would get %., which will now
	escape the character and be equal to its literal, which is
	a period, the .
--]]
```

+: Matches 1 or more repetitions, example:

```lua
--[[
	Let's get the age from this string.
	The pattern will match AT LEAST ONE digit.
	If no digit is present then it will return nil.
--]]

print(("Age: 2"):match("%d+"))
--> 2

print(("Age: 48"):match("%d+"))
--> 48

print(("Age: 101"):match("%d+"))
--> 101

print(("Age:"):match("%d+"))
--> nil

-- This is better than having to do multiple %d's huh?
```

*: Matches 0 or more repetitions while getting the longest possible match, example:

```lua
local String = "010 01010 01110 00"
print(String:match("0.*0")) -- Recall 0 is a literal since it isn't a magic character
--> 010 01010 01110 00

--[[
	How does the above string pattern work?
	Well, the pattern matches something that
	starts with a 0 and ends with a 0,
	and in the middle we have .* which means
	it will match anything in between those
	two zeros. Recall that . is a magic
	character that matches any character.
--]]

local String = "Age: 100"
print(String:match("Age: %d*"))
--> Age: 100

local String = "Age: 10"
print(String:match("Age: %d*"))
--> Age: 10

local String = "Age: 20"
print(String:match("%d*"))
-->

local String = "Age: "
print(String:match("Age: %d*"))
--> Age:

--[[
	For the last example it doesn't return nil
	because * checks if it's 0 or more, not 1 or more.
	If it were %d+ then it would return nil since there
	is not at least one digit to be matched.

	The third example prints an empty string because *
	matches ZERO or more. Since the first thing the
	pattern finds is ZERO of the pattern you specified
	it returns an empty string since something WAS matched,
	but it wasn't the match were looking for so it returns
	an empty string as opposed to nil.
--]]
```

-: Matches 0 or more repetitions while getting the shortest possible match, example:

```lua
local String = "010 01010 01110 00"
print(String:match("0.-0"))
--> 010

--[[
	This only matches 010 like we'd expect because
	- matches the shortest possible amount as opposed
	to * which matches the longest.
--]]
```

?: Matches 0 or 1 repetitions, example:

```lua
local String = "egg 010 dog"
print(String:match("0.?0"))
--> 010

local String = "cat0t0animal"
print(String:match("0.?0"))
--> 0t0

local String = "01110"
print(String:match("0.?0"))
--> nil

local String = "00"
print(String:match("0.?0"))
--> 00

--[[
	In the above examples recall that .
	is a magic character that matches
	any character. Since ? only matches
	0 or 1 repetitions that means
	.? will match at most one character.
	In the first and second examples,
	the pattern searches the string
	for a value that starts with a
	0 and ends with 0 and has a maximum
	of one character in between.
	The last example has two characters,
	but since it starts and ends with
	a 0 it is matched because the ?
	magic character also checks if there
	is no match. As for the third example,
	there is nothing in the string that has
	a starting 0 and an ending 0 that
	has at MOST one character in between them.
	So since there is no match, it returns nil.
--]]
```

Captures: these encapsulate a pattern and are returned if found in the pattern. For example:

```lua
--[[
	Let's initialize our string that we want to match.
	For this scenario we want to match the weight.
	If we were to use the pattern %d+, this would
	match the first set of numbers, which is 21.
	But we want the weight, not the age.
--]]

local String = "Age: 21; Weight: 100 lbs"

--[[
	Remember: we are using %d+ so it matches any
	amount of digits if there is at least one present.
	If there are none present it will return nil.
--]]

print(String:match("Weight: %d+"))
--> Weight: 100

--[[
	Why does this work? Well to start,
	we are matching 8 literals before
	we match the number. Those literals
	are, W, e, i, g, h, t, :, and a space.
	Remember, literals are non-magic characters
	that can be matched EXACTLY. So if our
	pattern was "weight: %d+" it would NOT work
	because we are trying to match a lower case
	w. The same thing can be done for age:
--]]

print(String:match("Age: %d+"))
--> Age: 21

--[[
	However, we are matching the FULL term,
	what if we just wanted the number? This
	is where captures come into play.
--]]

print(String:match("Weight: (%d+)"))
--> 100

print(String:match("Age: (%d+)"))
--> 21

--[[
	The capture, the () will take that value
	found (if found) and return it. There can
	many captures as well, for example:
--]]

local age, weight = String:match("Age: (%d+); Weight: (%d+)")
print(age, weight)
--> 21 100
```

Sets: this allows you to create your own character sets. For example:

```lua
--[[
	Let's create a set that will match vowels.
	Then we will use gsub to see how many vowels
	are in a given string.
--]]

local VowelsSet = "[aeiou]" -- This is also the pattern in our case
local String = "the dog jumped over the moon and di...d good"
local newString, count = String:gsub(VowelsSet, "")
print(newString)
--> th dg jmpd vr th mn nd d...d gd

print(count)
--> 13

--[[
	Whoa, this "gsub" thing is new, what is it?
	Allow me to explain, gsub is a replacement
	function. In our case, we pass two arguments
	to gsub, the VowelsSet (which is also the pattern),
	and what we want to replace each occurrence with.
	The pattern checks for any letter that is a
	LOWER CASE a, e, i, o, u. This is the power
	of sets. You can create your own *set* of
	characters. If a letter in our set matches
	a letter in the string, that letter in the
	string is replaced with whatever we passed
	as our replacement argument. In this case
	it doesn't matter because we only worry about
	the amount of (lower case) vowels are in the
	string. The first thing gsub returns is the new
	string. It is the string that has been replaced.
	As you can see above, the replaced the vowels with
	nothing, an empty string, so this effectively removes
	the vowel. The second return value of gsub is the
	amount of occurrences that have been removed. In this
	case we had 13 vowels, and they have been removed.
--]]
```

### Anchors
Phew, this is a lot of content. I know, sorry about that, but I want to cover just about everything I can. The next topic I want to go over are anchors. There are two anchors, the ^ and $. These anchors have specific meanings, th ^ means to start the match at the BEGINNING of the string, and the $ means to start the match at the END of the string. For example:

```lua
print(("010011"):match("^%d+"))
--> 010011

print(("a010011"):match("^%d+"))
--> nil

print(("a010011"):match("%d+"))
--> 010011

--[[
	The ^ anchor starts the match at the beginning
	of the string, if you don't have the anchor then
	the match will try to match anywhere in the string.
	This is why both example 1 and 2 print "010011".
	The first example, we have the pattern "^%d+"
	the anchor at the beginning tells the pattern
	to start the match at the beginning of the string.
	The %d+ then matches at least one digit or else
	it will return nil. The reason the second example
	returns nil is because the string starts with a letter,
	not a number. However, in the third example, the reason
	the numbers are matched is because we omitted the ^.
	Without the ^ the pattern checks for a match ANYWHERE
	in the string. This is the same $, except with $ it
	checks for a match at the end of the string. For instance:
--]]

print(("010011"):match("%d+$"))
--> 010011

print(("010011a"):match("%d+$"))
--> nil

print(("010011a"):match("%d+"))
--> 010011

--[[
	This is the same code as above, just with a $ instead
	of a ^, and the a is at the end of the string instead.
	Note how the $ anchor goes at the END of the pattern,
	whereas the ^ anchor goes at the START of the pattern.
--]]
```

## Examples
This section is going to serve as a numerous amount of examples, surprising, right?

### Phone Number
In this example I want to match for a phone number. So, we know what a phone number looks in the United states. It's the area code, followed by a dash (-), another three numbers, dash, and then four numbers. It looks something like this: **(555)-100-5555** or sometimes like **555-100-5555.** We have to take this into account. So, since we KNOW the area code is three numbers, and the second part of the number is three numbers and the last part is four numbers, we can make this match easier.

```lua
local PhoneNumber = "(555)-100-5555"
local Pattern = "(%d%d%d)-%d%d%d-%d%d%d%d"
print(PhoneNumber:match(Pattern))
--> nil
```

Whoa, wait a rootin tootin second! Why did it print nil? Maybe you noticed a few magic characters. Recall some of our magic characters, **(** **)** and **-** which we have in our pattern. Also recall the way we handle these magic characters, we escape them with **%.** We have to do this with every magic character we see. This will turn the character into its literal representation and will now match EXACTLY that character.

```lua
local PhoneNumber = "(555)-100-5555"
local Pattern = "%(%d%d%d%)%-%d%d%d%-%d%d%d%d"
print(PhoneNumber:match(Pattern))
--> (555)-100-5555
```

We escaped both parentheses and both of the dashes (hyphens, whatever you want to call them).

This is fine and dandelion, but what if we see the other representation of the number? 555-100-5555, this wouldn't work because our pattern is explicitly checking for a "(" and ")". Recall we have another magic character, the ?. This magic character allows us to check if there is zero or one occurrence which is exactly what we need. Allow the code to explain:

```lua
local PhoneNumber1 = "(555)-100-5555"
local PhoneNumber2 = "555-100-5555"
local Pattern = "%(?%d%d%d%)?%-%d%d%d%-%d%d%d%d"

print(PhoneNumber1:match(Pattern))
--> (555)-100-5555

print(PhoneNumber2:match(Pattern))
--> 555-100-5555
```

It worked! So, the ? checks if there is zero or one occurrence of a "(" and a ")". Since the second phone number has none this is fine and doesn't match it. What if we don't use hyphens and instead use spaces for our phone number? We can handle this too. Granted, it is very ugly, but we can do it. Recall how there is another pattern that matches whitespace, this is the %s pattern.

```lua
local PhoneNumber1 = "(555)-100-5555"
local PhoneNumber2 = "(555) 100 5555"
local PhoneNumber3 = "555-100-5555"
local PhoneNumber4 = "555 100 5555"
local Pattern = "%(?%d%d%d%)?[%s-]?%d%d%d[%s-]?%d%d%d%d"

print(PhoneNumber1:match(Pattern))
--> (555)-100-5555

print(PhoneNumber2:match(Pattern))
--> (555) 100 5555

print(PhoneNumber3:match(Pattern))
--> 555-100-5555

print(PhoneNumber4:match(Pattern))
--> 555 100 5555
```

Man! That's a mess, let's explain what we did. We used the %s pattern to match whitespace, in this case a space. We used a set to check for the occurrence of a space or a hyphen. At the end of the set we used a ? to check for zero or one of those occurrences. Technically, you can also make it all one number and it'll match because of how we set up the pattern, but let's ignore that edge case. Another problem is the fact %s matches not only a space but other characters as well. The characters %s matches are:

- 9 - TAB (horizontal tab, \t)
- 10 - LF (line feed, new line, \n)
- 11 - VT (vertical tab)
- 12 - FF (form feed, new page)
- 13 - CR (carriage return)
- 32 - Space

If we want to match the exact space character we can simply change the pattern to:

```lua
local Pattern = "%(?%d%d%d%)?[ -]?%d%d%d[ -]?%d%d%d%d"
```

Notice how the %s is now just a space. Since the space character is inserted, we are using it as a literal (because the space character is it's own literal just like a is a literal). This will only match a space (byte 32) now as opposed to any of the whitespace characters.

After this example I hope this shows how escape characters, the ? magic character, and sets can be useful. Now onto the next example!

### Separating a Word in PascalCase
Have you ever needed to separate a word in PascalCase? If not then that's not very surprising. First let me explain what PascalCase is, it's words that are all one word, but each word is capitalized example: *ThisVariableIsPascalCase.* Let's say you wanted to separate the word into its individual words. You can accomplish this with string patterns and using **gmatch.** gmatch is another function of strings that returns an iterator (not going into detail about what iterators) are. Here is the example:

```lua
local String = "ThisIsAPascalCaseStringLol"

for word in String:gmatch("%u%l*") do
	print(word)
end

--> This
--> Is
--> A
--> Pascal
--> Case
--> String
--> Lol
```

What happens is each iteration it checks for ONE upper case letter followed by 0 or more lower case letters. Once it reaches another upper case letter it stops because it is not a lower case letter and then iterates again. This keeps matching until the match returns nil. The reason we used * instead of + was because of words that are only one character long (like I, A, etc).

### List of Control Characters
The ASCII representation of each control character is: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 127. [Click here to learn what ASCII is.](http://www.asciitable.com/)

- 0 - NUL (null)
- 1 - SOH (start of heading)
- 2 - STX (start of text)
- 3 - ETX (end of text)
- 4 - EOT (end of transmission)
- 5 - ENQ (enquiry)
- 6 - ACK (acknowledge)
- 7  - BEL (bell)
- 8 - BACKSPACE
- 9 - TAB (horizontal tab)
- 10 - LF (line feed or new line)
- 11 - VT (vertical tab)
- 12 - FF (form feed, new page)
- 13 - CR (carriage return)
- 14 - SO (shift out)
- 15 - SI (shift in)
- 16 - DLE (data link escape)
- 17 - DC1 (device control 1)
- 18 - DC2 (device control 2)
- 19 - DC3 (device control 3)
- 20 - DC4 (device control 4)
- 21 - NAK (negative acknowledge)
- 22 - SYN (synchronous idle)
- 23 - ETB (end of transmission block)
- 24 - CAN (cancel)
- 25 - EM (end of medium)
- 26 - SUB (substitute)
- 27 - ESC (escape)
- 28 - FS (file separator)
- 29 - GS (group separator)
- 30 - RS (record separator)
- 31 - US (unit separator)
- 127 - DEL (delete)
