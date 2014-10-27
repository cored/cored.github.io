---
layout: post
title: "Code Read: Dotenv part 3"
date: 2014-07-22 17:43
comments: true
categories: ["CodeClub", "Dotenv"]
---

This is the final post on reading Dotenv code which was quite fun and very
insightful. Let me break it down into some smaller pieces. 

TLDR;

I already cover the entry points now is the turn for the inner pieces of this
project. 

```ruby
require 'dotenv/substitutions/variable'
if RUBY_VERSION > '1.8.7'
  require 'dotenv/substitutions/command'
end

module Dotenv
  class FormatError < SyntaxError; end

  class Parser
    @@substitutions = Substitutions.constants.map { |const| Substitutions.const_get(const) }

    LINE = /
      \A
      (?:export\s+)?    # optional export
      ([\w\.]+)         # key
      (?:\s*=\s*|:\s+?) # separator
      (                 # optional value begin
        '(?:\'|[^'])*'  #   single quoted value
        |               #   or
        "(?:\"|[^"])*"  #   double quoted value
        |               #   or
        [^#\n]+         #   unquoted value
      )?                # value end
      (?:\s*\#.*)?      # optional comment
      \z
    /x

    def self.call(string)
      new(string).call
    end

    def initialize(string)
      @string = string
    end

    def call
      @string.split("\n").inject({}) do |hash, line|
      if match = line.match(LINE)
        key, value = match.captures

        value ||= ''
        # Remove surrounding quotes
        value = value.strip.sub(/\A(['"])(.*)\1\z/, '\2')

        if $1 == '"'
          value = value.gsub('\n', "\n")
          # Unescape all characters except $ so variables can be escaped properly
          value = value.gsub(/\\([^$])/, '\1')
        end

        @@substitutions.each do |proc|
          value = proc.call(value, hash)
        end

        hash[key] = value
        elsif line !~ /\A\s*(?:#.*)?\z/ # not comment or blank line
          raise FormatError, "Line #{line.inspect} doesn't match format"
        end
        hash
      end
    end
  end
end
```
## What does this piece of code do?

Well it's just takes an ```Environment``` object which contains all the key
values pairs extracted from the files and start validating it. Basically that's
it. It uses some other piece of code which resides inside some value objects
which I will talk about them later. Why I think they are value objects? Well
for starter they are immutable and what they do is getting some data and
returning some output, doesn't handle any type of state; that's why.

## What did I learn from this piece of code?

