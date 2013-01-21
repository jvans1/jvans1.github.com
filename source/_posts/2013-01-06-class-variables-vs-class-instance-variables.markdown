---
layout: post
title: "Class Variables vs Class Instance Variables"
date: 2013-01-06 14:04
comments: true
published: true
categories: 
---




If you want data that only a class and it's instances have access to, class variables often seem like the best option. Constants don't really fit the bill because they're accessible outside of the class and instance variables don't work because they're not accessible to anyone other than that specific instance. If for example, you wanted to keep track of the number of number of bank accounts your bank has you might have a class that looks like this

```ruby
class BankAccount

  @@total_accounts = 0

  def initialize
    @@total_accounts+=1
  end  

  def self.total_accounts
    @@total_accounts
  end
end

b1 = BankAccount.new
b2 = BankAccount.new
b3 = BankAccount.new

BankAccount.total_accounts => 3
```
So everytime a new bank account is created, the class variable ```@@total_accounts``` increases by one and you can find the total by calling ```BankAccount.total_accounts```. Easy enough. 

Now say we develope our software a little further and add a new class ```BusinessAccount``` that inherits from ```BankAccount```. If you want to keep track of the number of business accounts your system has, you might be tempted to do something like this.

```ruby
class BusinessAccount < BankAccount
  @@total_accounts = 0 
end
```
 However now we run into a problem. Since class variables are shared among subclasses, setting ```@@total_accounts``` reassigns the variable to 0. 
```ruby
BankAccount.total_accounts => 0
```
  There are two solutions to this problem. You can either namespace all of your class variables so in your ```BusinessAccount``` class you use ```@@total_business_accounts```. This ends up becoming a little cumbersome, you end up with long variable names a lot of repeated code. A better solution is to use class instance variables. Refactoring the above code to use class instance variables looks like this.

```ruby
class BankAccount

  def initialize
    self.class.total_accounts+=1
  end  
  def self.total_accounts
    @total_accounts ||=0
  end
  def self.total_accounts=(n)
    @total_accounts +=1
  end
end

class BusinessAccount < BankAccount
end
b1 = BankAccount.new
bz1 = BusinessAccount.new
bz2 = BusinessAccount.new
BankAccount.total_accounts => 1
BusinessAccount.total_accounts => 2 
BankAccount.total_accounts => 1 
```

Since ```BusinessAccount``` Inherits from ```BankAccount```, it gets all of the methods for keeping track of the number of accounts created. The big advantage here lies in the difference between scope of class variables and instance variables. Since instance variables aren't shared between objects they're confined to their respective classes and keep count independantly. A big win by both using less code and avoiding mixing variables unintentionally. 