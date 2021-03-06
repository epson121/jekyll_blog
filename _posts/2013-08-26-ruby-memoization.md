---
layout: post
title: Ruby memoization
date: 2013-08-26 13:47:19.000000000 +00:00
type: post
categories:
- Ruby
tags:
- fibonacci sequence
- memoization
- optimization technique
- ruby
excerpt: "I've been going through Ruby Best Practices book lately and, while it covers
  a lot of interesting topics, one thing caught my eye. It was sub-chapter about memoization.\r\n\r\nMemoization
  is an optimization technique used to speed up your programs by avoiding to repeat
  function calls for already calculated inputs."
author: luka
---
I've been going through <a href="http://www.amazon.com/Ruby-Best-Practices-Gregory-Brown/dp/0596523009">Ruby Best Practices</a> book lately and, while it covers a lot of interesting topics, one thing caught my eye. It was sub-chapter about <a href="http://en.wikipedia.org/wiki/Memoization">memoization</a>.
Memoization is an optimization technique used to speed up your programs by avoiding to repeat function calls for already calculated inputs. Example in the book was a simple Fibonacci sequence program written with and without memoization. It was really shocking for me to see how big of an advantage memoization gives you. I rewrote the examples and made some benchmarks to see the actual results and improvements.

Here is a simple Fibonacci implementation in ruby:
{% highlight ruby %}
	# Standard implementation of fibonacci sequence
	def fibo_n n
		return n if (0..1).include? n
		fibo_n(n-2) + fibo_n(n-1)
	end
	p fibo_n 4 => 3
	p fibo_n 10 => 55
	p fibo_n 20 => 6765
{% endhighlight %}

Calling this function for larger inputs is not a good idea because for input 30 calculation lasts for 3 seconds, and it increases exponentially.
Simple benchmark for this function made using benchmark:
{% highlight ruby %}
	require "benchmark"
	N = 100
	Benchmark.bmbm do |x|
		x.report("fibo_n") do
			N.times {
				fibo_n(20)
			}
		end
	end
	=>
	Rehearsal ------------------------------------------
	fibo_n   2.440000   0.030000   2.470000 (  2.647973)
	--------------------------------- total: 2.470000sec
	             user     system      total        real
	fibo_n   2.340000   0.010000   2.350000 (  2.414726)
{% endhighlight %}

Using memoization for generating Fibonacci sequence means we are not going to calculate solutions for inputs we have already calculated. For Fibonacci sequence, this is an enormous gain in performance.
Simple implementation of fibonacci using memoization:
{% highlight ruby %}
	##
	# Memoizable implementation saves values in array for
	# later use
	@series = [0, 1]
	def fibo_memoized(n)
	  @series[n] ||= fibo_memoized(n - 1) + fibo_memoized(n - 2)
	end
	p fibo_memoized(1000)
	p fibo_memoized(2000)
{% endhighlight %}

With memoization, calculations for inputs like 30, 40 etc. are done instantaneously. Memoization also enables us to calculate values for inputs like 1000, 2000 etc. For example,
{% highlight ruby %}
	p fibo_memoized(2000)
	=> 42246963333923048787067256023414827825798528402506810980
	10280137314308584370130707224123599639141511088446087538909
	60360764019471164359602927198331259873732625355580260699158
	59152294924539049987222567953169828744824729922639018337167
	78060607011615497886719879858311468870876264597369086722884
	02365442229524334796448013951534956297208765265606952980649
	98419774487201556128026654045541717178819303240252043120825
	16817125
{% endhighlight %}

So, to summarize, using memoization you can speed up your programs by storing already calculated results in an array (or hash). This speed gain comes with a price. You need extra memory to hold all of the calculations, and for larger inputs you would need a lot of it.
Also there is this thing called SystemStack. And it can exceed its maximum depth fairly easy. For example, I get for very big numbers (> few thousands)
{% highlight ruby %}
	memoization_fibonacci.rb:18:in `fibo_memoized':
	stack level too deep (SystemStackError)
{% endhighlight %}

And the final benchmark to sum this up:
{% highlight ruby %}
	require "benchmark"
	N = 100
	Benchmark.bmbm do |x|
		x.report("fibo_n") do
			N.times {
				fibo_n(20)
			}
		end
		x.report("fibo_memoized") do
			N.times{
				fibo_memoized(20)
			}
		end
	end
	Rehearsal -------------------------------------------------
	fibo_n          2.310000   0.010000   2.320000 (  2.334365)
	fibo_memoized   0.000000   0.000000   0.000000 (  0.000109)
	---------------------------------------- total: 2.320000sec
	                    user     system      total        real
	fibo_n          2.320000   0.000000   2.320000 (  2.352468)
	fibo_memoized   0.000000   0.000000   0.000000 (  0.000077)
{% endhighlight %}
