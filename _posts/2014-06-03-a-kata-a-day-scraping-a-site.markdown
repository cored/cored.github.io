---
layout: post
title: "A kata a day: Scraping a site"
date: 2014-06-03 18:16
comments: true
categories: ["CodingKatas", "Ruby"]
---

Long time without writting about my coding kata practices; today I found an
assignment from a company it was meant to be done in Python and probably that
will be my next thing to do just to figure out how to use another environment
and programming language will be quite interesting.

Here goes the assignment:

```
We need you to do some scraping. The objective is to create a script that connects
to [Diario Libre](http://www.diariolibre.com) or [El Caribe](http://www.elcaribe.com.do)
to collect the exchange rate for US$ and Euro.
```

I decided to do this with mini test just to mix it up a little bit; here goes
the spec:

```ruby
describe Scrapy do
  before do
    @scrapy = Scrapy.new
  end

  describe '#rate' do
    it 'returns the euro exchange rate' do
      @scrapy.rate.must_equal euro: 59.93, dollar: 43.26
    end
  end
end
```

My first implementation:

```ruby
class Scrapy
  URL = 'http://www.elcaribe.com.do'

  def initialize
    @page = Nokogiri::HTML(open(URL))
  end

  def rate
    rate = {}
    rate[:euro] = @page.css('div.dolar span').children[1].text.split(/\$/)[1].to_f
    rate[:dollar] = @page.css('div.dolar span').children[0].text.split(/\$/)[1].to_f
    rate
  end

end
```
Pretty straight forward stuff, I just went to the site and see the source of
the html to see where the values that I needed reside. But I wasn't happy with
this so I did a little bit of refactoring and the following was my final
solution:

```ruby
class Scrapy
  URL = 'http://www.elcaribe.com.do'

  def initialize
    @page = Nokogiri::HTML(open(URL))
  end

  def rate
    Rate.for(@page.css('div.dolar span')).to_h
  end
end

class Rate
  def self.for(data)
    new(data.children)
  end

  def initialize(rate_data)
    @dollar = Dollar.for(rate_data[0].text)
    @euro = Euro.for(rate_data[1].text)
  end

  def to_h
    { euro: @euro, dollar: @dollar }
  end

  class Dollar
    def self.for(dollar)
      dollar.split(/\$/)[1].to_f
    end
  end

  class Euro
    def self.for(euro)
     euro.split(/\$/)[1].to_f
   end
  end
end
```

This looks better to me there are more classes of course but it also hides
a lot of complexities in it's smaller pieces easier to follow; next step was to
use the first spec as an integration test and just mock it up to test the
smaller pieces the advantages of this is that I won't need to be hitting the
html everytime and if at some point the html changes the integration test will
tell me so.

## What did I learn with this Kata?
* Always expose domain concepts
* Start with an integration tests and then separate the logic in smaller pieces
to test it out in isolation.
