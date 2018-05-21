---
layout: post
title: String manipulation in Ruby
date: 2013-09-01 14:55:33.000000000 +00:00
categories:
- Ruby
tags:
- EOM
- ruby
- string
author: luka
excerpt: Strings in Ruby are, just like in other programming languages, objects for
  handling text.  If you ever tried to write some Ruby code, you are familiar with
  them and their syntax. But this post is not about syntax that is well known. While
  reading through some ruby books and tutorials I found some pretty interesting ways
  of dealing with strings.
---
Strings in Ruby are, just like in other programming languages, objects for handling text.  If you ever tried to write some Ruby code, you are familiar with them and their syntax.

```ruby

"This is a double-quoted string."

'This is a single-quoted one.'

```

Simple. :)

But this post is not about syntax that is well known. While reading through some ruby books and tutorials I found some pretty interesting ways of dealing with strings.

### string creation

Strings can be made, besides using single or double quotes, with notation like this one:

```ruby

%char{text}

```

Here, char is replaced by one of these characters:

    q - for single-quoted strings,
    Q for double-quoted or
    char can be omitted to also create double-quoted strings.

```ruby

>> %q{Single quoted string.}
=> 'Single quoted string.'
>> %Q{Double quoted string.}
=> "Double quoted string."
>> %{Double quoted string.}
=> "Double quoted string."
```

Also, we can replace the curly braces with any other character and still get the string object, like this.

```ruby
>> %q[String with square braces.]
=> "String with square braces."
>> %q-String with minuses-
=> "String with minuses"
```

### multiline strings

Sometimes you want your strings to span across multiple lines..maybe.. Fortunately this also can be easily done in Ruby. Syntax is as follows:

```ruby
>> string = <<EOM
Line1
Line2
Line3
EOM
=> "Line1\nLine2\nLine3\n"
```

Key thing is the << operator. Using it with the uppercase text (like EOM, which means End of message, but any other works too) at the end of the first line you have the ability to type your text across as many lines as you wish, until you hit another EOM again (or any other word of your choosing). EOM has to be on its own line, flushed left, alone.

By default, text entered between EOM strings is read in double-quoted strings. For reading the text in single-quoted strings you need to do something like this:

```ruby
string = <<'EOM'
Line1
Line2
Line3
EOM
=> "Line1\nLine2\nLine3\n"
```

String is now enclosed in single quotes.

### substrings

To access a character in Ruby string, you usually do something like this:

```ruby
>> a = "String"
>> a[0]
=> 83
```

But to get a substring of, say, 3 letters from the string above we simply do:

```ruby
>> a = "String"
>> a[0, 3]
=> "Str"
```

where the second argument to [] method is number of letters to grab starting at the number in first argument. There is also a well known method like slice()

### string interpolation

Interpolating string means putting an existing string into a new string, like this:

```ruby
>> a = "String"
>> b = "Are you a #{a}?"
=> "Are you a String?"
```

By using #{expression} notation, we can embed any Ruby expression inside new string. Here is a fun example:

```ruby

>> a = "I am #{class User
attr_accessor :name
end
n = User.new()
  n.name = "Frodo Baggins"
  n.name
}"

>> puts a
=> "I am Frodo Baggins."

```

Cool, right? :)

### querying strings

We can query string using built-in methods to find its content, for example, counting the letters in string:

```ruby
>> a = "This is string."
>> a.count("i")
=> 3
>> a.count("is")
=> 6
```

Count the letters in range:

```ruby
>> a = "This is string."
>> a.count("a-i")
=> 5
```

Or count all the letters, except the given ones:

```ruby
>> a = "This is string."
>> a.count("^is")
=> 9
>> a.count("^a-i")
=> 10
```

You can also combine letters and ranges, along with query chaining:

```ruby
>> a = "This is string."
>> a.count("sa-i")
=> 8
>> a.count("sa-i", "^hg")
=> 6
```
### string transformations

You can easily change case of you string by using

```rubyupcase, downcase and swapcase```

methods.

```ruby
>> a = "This is string."
>> a.upcase
=> "THIS IS STRING."
>> a.downcase
=> "this is string."
>> a.swapcase
=> "tHIS IS STRING."
```

There are so many interesting methods and techniques in Ruby regarding the string manipulation. This was only a portion of it. I will try to write a sequel to this and add many more examples.