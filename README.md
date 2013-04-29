# ScopedAttrAccessor

Adds scoped accessors to ruby. You can create writers, readers and
accessors in either private or protected scope. Does not affect the
scope of code that comes afterward. You can extend a single class (and
its subclasses) with scoped accessors, or you can extend ruby's entire
class hierarchy.

## Why Oh Why Does This Exist?!?

At the time of this writing (April 2013), the ruby community in
general is somewhat divided on the meaning of the concept of privacy
in ruby. For some, especially those who come from Java, C++, VB, or
some other language that enforces encapsulation with privacy, ruby's
ability to get around privacy means that encapsulation cannot be
enforced. Most of us (I count myself in this camp) have discarded the
use of privacy in ruby altogether, as if it were nothing more than a
half-hearted nod to other languages' encapsulation practices. Make
everything public, since everything's ultimately public anyway, we
say; using the private keyword is merely a speedbump which adds no
security but does add hassle and headache.

I am becoming more and more aware of a number of rubyists who consider
the use of private and protected methods in ruby to be a useful way to
communicate to the reader that the code is highly volatile, under
question, subject to change or outright removal, etc., and that for
these reasons the code should not be surfaced to the public API of the
class. They ALSO consider the private keyword to be merely a
speedbump, but one which communicates that maybe you should slow down
a bit before trying to access these portions of code. Furthermore,
making these methods private or protected makes them "exemplary" to
maintainers of the code, meaning that a maintainer will understand the
intent of the privacy, and be less likely to *accidentally* create an
external dependency on on a method that should have been kept private.

I am becoming more and more swayed by this second way of thinking, but
I find that it falls short in one key area: accessors. It is easy to
create a private method in ruby, but creating a private accessor
method is actually a bit tortuous. As a result, I see programmers who
strongly believe in using privacy to communicate undependable
interfaces frequently making use of either instance variables or
public accessors for their private variables. These variables are not
dependable, at least from a "stable interface" standpoint, so both
solutions offer an unfavorable tradeoff. While instance variables
communicate privacy, they force the object to depend on its own
undependable internal interface. Meanwhile, public accessors allow the
class to isolate itself from its undependable interface, but by making
it public the communication to the maintainer is that other classes
can and should depend on that undependable interface.

The reason programmers make this tradeoff is that there doesn't seem
to be an easy, clean, *elegant* way to create private and protected
accessors in ruby. My goal with this gem, then, is to create such a
way, so that those who wish to communicate "this variable is
undependable and it should be kept isolated for now" can do so easily.

## Installation

Add this line to your application's Gemfile:

    gem 'scoped_attr_accessor'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install scoped_attr_accessor

## Usage

You can add scoped accessors to a single class (and its children) by
directly extending `ScopedAttrAccessor` in your class:

    require 'scoped_attr_accessor'

    class Primate
      extend ScopedAttrAccessor
      private_attr_accessor :some_weird_primate_only_counter
      protected_attr_reader :some_weird_counter
    end

    class Monkey < Primate
      # Monkey can define its own scoped accessors because Primate
      # extended the module.
      private_attr_reader :get_weird_monkey_only_stuff_here
      private_attr_writer :put_weird_monkey_only_stuff_here

      # use our inherited, protected reader
      def sufficiently_weird?
        some_weird_counter > 42
      end
    end

Alternately, if you require `scoped_attr_accessor/include`, ruby's
`Object` class will be extended with `ScopedAttrAccessor` making all
classes able to have protected and private accessors.

    require 'scoped_attr_accessor/include'

    class Primate
      private_attr_accessor :private_primate_counter
    end

## Avoid Dependency Infection

The '/include' form of this gem is a ruby-wide monkeypatch. Please
remember that it's perfectly fine to use it this way in your own
applications, but quite rude to use it this way in a library.

If you use scoped accessors in a gem or library of your own, please
consider using the non-include version, or, in Ruby 2.0, use
refinements to confine the include patch to your library.

## Ruby Versions

Tested to work on Ruby 1.9.3 and 2.0.

## Contributing

1. Fork it
1. Create your feature branch (`git checkout -b my-new-feature`)
1. Write tests for your feature or bug fix
1. Commit your changes (`git commit -am 'Add some feature'`)
1. Push to the branch (`git push origin my-new-feature`)
1. Create new Pull Request