# Temping

## Description

Temping allows you to create arbitrary ActiveRecord models backed by a temporary SQL table for use in tests.

Use cases for this include:

* Testing a module that extends an ActiveRecord::Base-derived model without depending on a concrete Class in your application that's subject to change or disappear
* Tests for ActiveRecord extending plug-ins that must be tested against an ActiveRecord::Base-derived Class

Temping will use your existing database connection. If one does not exist, it will create and connect to an in-memory SQLite3 instance.

As we're using temporary tables all data will be dropped when the database connection is terminated.

## Examples

The basic setup of a model involves calling _create_model_ with a symbol that represents the plural table name of the model you wish to create. By default, this will create a temporary table with an _id_ column.

    require 'temping'
    include Temping
    
    create_model :dog
    
    Dog.create => #<Dog id: 1>
    
Additional database columns can be specified via the _with_columns_ method which uses Rails migration syntax:

    require 'temping'
    include Temping
    
    create_model :dog do
      with_columns do |t|
        t.string :name
        t.integer :age, :weight
      end
    end
    
    Dog.create => #<Dog id: 1, name: nil, age: nil, weight: nil>
    
When a block is passed to _create_model_, it is evaluated in the context of the ActiveRecord class. This means anything you do in an ActiveRecord model can be accomplished in the block including method definitions, validations, module includes, etc.

    require 'temping'
    include Temping
    
    create_model :dog do
      include Duckish

      with_columns do |t|
        t.string :name
        t.integer :age, :weight
      end
      
      validates_presence_of :name
      
      def quack
        "arf!"
      end
    end
    
    Dog.create! => ActiveRecord::RecordInvalid: Validation failed: Name can't be blank
    codey = Dog.create!(:name => 'Codey')
    codey.quack => "arf!"
    
## Installation

    jp@populuxe:~$ gem install temping

## Bugs, Features, Feedback

Tickets can be submitted by via GitHub issues.

## LICENSE:

(The MIT License)

Copyright (c) 2009-2010 John Pignata

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
