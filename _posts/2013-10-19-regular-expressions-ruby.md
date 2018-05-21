---
layout: post
title: Regular expressions (Ruby)
date: 2013-10-19 15:40:45.000000000 +00:00
categories:
- Ruby
tags:
- regex
- ruby
author: luka
excerpt: Things you should know about ruby regular expressions.
---
Things you should know about ruby regular expressions:
```ruby
  # Regular expressions use // (slash) symbols.
  p //.class
  => Regexp
  
  # You can check if string matches regular expression and vice versa
  p true if /abc/.match("abc")
  => true
  p true if "abc".match(/abc/)
  => true 
  
  # You can also use ~= operator instead of .match()
  p "abc" =~ /abc/
  p /abc/ =~ "abc"
  
  # To match any character use the . (dot)
  p /./ =~ "a"
  => 0
  p /./ =~ "-"
  => 0
  
  # To match one of many characters use [ ]
  p /[abcd]/ =~ "a"
  p /[abcd]/ =~ "c"
  p /[a-z]/ =~ "k"
  p /[A-Za-z0-9]/ =~ "8"
  p /[A-Za-z0-9]/ =~ "a"
  p /[A-Za-z0-9]/ =~ "G"
  => 0
  
  # To negate a character in search, ie. search all that is NOT this character, use ^ (caret) symbol
  p /[^a]/ =~ "abc" # => 1
  p /[^a-zA-Z0-9]/ =~ "abcABC012" # => nil
  
  # You can match certain other types of characters using this handy notation:
  # \w -> any digit, character or underscore
  # \s -> whitespace
  ## Negated versions
  # \D -> any character that is not a digit
  # \W -> any character that is not alfanumeric and/or underscore
  # \S -> everything but whitespace
  p /\d/ =~ "9"
  => 0
  p /\d\d\d\d/ =~ "1999"
  => 0
  
  # You can group substrings using ( ) parentheses.
  # for example, string is: "04. 09. 2013."
  p /(\d\d\.)\s(\d\d\.)\s(\d\d\d\d\.)$/ =~ "04. 09. 2013." # => 0
  p $1 # => "04."
  p $2 # => "09."
  p $3 # => "2013."
  
  # or
  date = "04. 09. 2013.".match(/(\d\d\.)\s(\d\d\.)\s(\d\d\d\d\.)$/)
  
  # if successful
  whole_string = date[0]
  day = date[1]
  month = date[2]
  year = date[3]
  p day + month + year # => "04.09.2013."
  
  # all matching groups in one array:
  p date.captures # => ["04.", "09.", "2013."]
  
  # You can grab characters before and after the matched text:
  m = "Today is 04. 09. 2013. and tommorow is not.".match(/(\d\d\.)\s(\d\d\.)\s(\d\d\d\d\.)/)
  p m.pre_match # => "Today is "
  p m.post_match # => " and tommorow is not."
  
  # Greedy quantifiers + and * eat up as much characters as they can:
  string = "abc!def!ghi!"
  match = /.+!/.match(string)
  p match[0] # => "abc!def!ghi!"
  
  # To remove 'greedyness' you put ? (question mark) after the quantifier:
  match = /.+?!/.match(string)
  p match[0] # => "abc!"
  
  # You can specify number of repetitions per match like this:
  ## number of repetitions is presented with {num} expression
  p "1234" =~ /\d{4}/ # => 0 (index)
  p "123.432.asd" =~ /\d{3}\.\d{3}\.\w{3}$/ # => 0 (index)
  
  # you can also use ranges
  p "1" =~ /\d{1,10}/ # => 0
  p "1342234" =~ /\d{1,10}/ # => 0
  
  # Regular expressions have "modifiers". They appear after the last / , like /i or /m . You can use them like this:
  # i -> case insensitive match
  # m -> . will match everything including the newline "\n" character
  # x -> ignore whitespace that is not explicitly added with \s (good for comments)
  date_regex = / # start of the regex
  (\d\d\.) # day match group
  \s # whitespace
  (\d\d\.) # month match group
  \s # whitespace
  (\d\d\d\d\.)$ # year match group
  /x # => comments are possible because of this x at the end
  date_regex.match("04. 09. 2013.")
  p $1 # => "04."
  p $2 # => "09."
  p $3 # => "2013."
  
  # You can use regexes with ruby methods such as scan, split, gsub and sub
```