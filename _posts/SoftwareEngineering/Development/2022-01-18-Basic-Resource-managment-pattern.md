---
title : "Resource ManageMent"

excerpt: "Pattern in Resource Mangement"

categories:
  - Development
tags:
  - [SoftwareEngineering , Resource Management]
# classes : wide
toc: true
toc_sticky: true
---

When we create a program, we manage a lot of resources. These resources include memory , transaction ,thread , network connecting , file and more. All of these have limits. However, resource usage mostly follows a predictable pattern. Nevertheless, most developers do not consistently handle patterns when managing resources. Today, we want to learn about popular patterns.



## Basic Resource management pattern

There is a pattern that applies in most situations when managing resources. That is, making the allocator responsible for the deallocation. That is, when the allocator allocates and uses a resource, it must always be the allocator that returns the resource being used. If the allocator does not use a consistent pattern responsible for deallocation, and a third party takes responsibility for allocating resources, at some point the resource will become unavailable. Let's take a closer look at this with an example program.



### Common resource management mistake

```ruby
def read_customer
 @customer_file = File.open(@name + ".rec", "r+")
 @balance = BigDecimal(@customer_file.gets)
end
def write_customer
 @customer_file.rewind
 @customer_file.puts @balance.to_s
 @customer_file.close
end
def update_customer(transaction_amount)
 read_customer
 @balance = @balance.add(transaction_amount,2)
 write_customer
end
```

Let's say you have such a Ruby program. A program that updates the balance in a user's account when a transaction occurs in the banking business. Looking more closely at the program, `read_customer` opens the file and `write customer` closes the file. However, this program has a fatal problem. It's just that they're so interconnected. A resource called `customer file` is shared by read and write. And, it is update that makes a request for `customer file`, but `customer file` does not appear in the update program. Having such a shared variable causes problems. Let's look at an example below.

```ruby
def update_customer(transaction_amount)
 read_customer
    if (transaction_amount >= 0.00)
     @balance = @balance.add(transaction_amount,2)
     write_customer
    end
end
```

The program has been modified to change the balance in the `customer file` only when the `transaction amount` is 0 or more due to changes in the program restrictions. In this way, it can be judged that there is no problem because the intended function works well during the test. However, when it actually goes into production, the program will collapse. Perhaps, you will see an error saying `too many open file`.

This program opens a `customer file`, changes the balance and closes the file only when `transaction amount` is greater than 0. In other words, even if `transaction amount` is not 0 or more, file open and close are conditional.



### Responsible for deallocation

```ruby
def read_customer
 @customer_file = File.open(@name + ".rec", "r+")
 @balance = BigDecimal(@customer_file.gets)
end
def write_customer
 @customer_file.rewind
 @customer_file.puts @balance.to_s
 @customer_file.close
end
def update_customer(transaction_amount)
 read_customer
if (transaction_amount >= 0.00)
 @balance += BigDecimal(transaction_amount, 2)
 write_customer
else
 @customer_file.close # Bad idea!
end
end
```

The code above solved the problem. Always close the file whether `transaction amount` is greater than or equal to 0. Now, `update customer` is requesting allocation and requesting deallocation. However, this is not enough. This is because the file is a shared variable for read and write. In this case, it will be complicated because tracking who opens and closes the file goes through several steps. And, it is not ideal deallocation.

### Not shared with multiple routines

```ruby
def read_customer(file)
 @balance=BigDecimal(file.gets)
end
def write_customer(file)
 file.rewind
 file.puts @balance.to_s
end
def update_customer(transaction_amount)
 file=File.open(@name + ".rec", "r+") # >--
 read_customer(file) # |
 @balance = @balance.add(transaction_amount,2) # |
 write_customer(file) # |
 file.close # <--
end
```

 The `update customer` should be the subject of allocating and releasing resources. Changed code so `udpate customer ` allocates and frees resources. Now all responsibility for the reosource is on the `udpate customer`. The file is opened on `udpate customer`, the balance is changed and the file is closed at `update customer`.



## Need one more resource

Above, we saw a basic pattern where the person who requested resource allocation is responsible for the deallocation. Previously, it was a pattenr that deallocates when a resource is allocated. From now on, this basic pattern can be extended to a pattern that requests another resource while using one resource. Even so, the pattern discussed above does not change. There are a few variations, but there are two ways.

- Deallocation starts with resources allocated later.
  - In this way, the resource is not orphaned even if there is a reference to another resource.
- Even if the same resource is allocated to different spaces, the rules must be followed.
  - When there are processes A and B, if A requests B's resource and B requests A's resource, the process will wait forever. This is called a deadlock. A must allocate the resource it has, and B must also allocate the resource it has. This will further reduce the chance of deadlock.

When someone allocates, the basic rule of deallocation does not change. Even from a programming language point of view, you can see that this always applies.



### Object Resource management

If you look at Objected-Oriented Programming, you can see that it is useful for a class to encapsulate a variable and store it. When a class instance is created, a resource is allocated to a variable in the class. And, when the instance goes out of scope, the descontructor descontruc it and the garbage collector reclaims the resource.

Calling a deconstructor when an object is a constructor and goes out of scope is equivalent to an allocator responsible for deallocation.



### Exception Resource management

Some programming languages support exceptions. However, exceptions make the resource deallocation process more complex. For example, how do you know if the resource was deallocated before the exception? In this case, you have two options.

1. variable scope
2. try .. finally.. catch

In the scoping rule of languages such as C++ or Rust, when a return is returned outside the scope, desconstructor is called as a hook function of varialbe and the resource is deallocated.

```rust
{
let mut accounts = File::open("mydata.txt")?; // >--
// use 'accounts' // |
 ... // |
} // <--
// 'accounts' is now out of scope, and the file is
// automatically close
```

Here, the account file is automatically deallocated when it goes out of scope.

Another option is the try, catch syntax.

```
try
// some dodgy stuff
catch
// exception was raised
finally
// clean up in either case
```

However, there are some caveats when using try and catch.

```
begin
 thing = allocate_resource()
 process(thing)
finally
 deallocate(thing)
end
```

What happens if allocation fails and deallocate is done? The deallocation will be requested even though the resource has not been allocated.

```
thing = allocate_resource()
begin
 process(thing)
finally
 deallocate(thing)
end
```

The correct pattern is to separate allocation and deallocation from try and finally.

## Basic pattern not applied

So far, we have looked at basic patterns for managing resources. However, there are times when these patterns are not appropriate. That's when you use dynamic datastructure. In a dynamic datastructure, when a routine allocates memory, the large datastructure will point to it. The solution here is to determine which semantic invarints and who will be responsible for the deallocation. When releasing the top-level datastructure, one of the following options is selected and followed.

1. The top-level structure is responsible for the deallocation of resources possessed by the substructure.
   1. Recursive deallocation of data of substurcture.
2. Just release the top-level structure.
   1. Resources referenced by top-level structures are orphaned.
3. If the substructure is allocating resources, the top-level structure rejects the deallocation.

There is absolutely nothing good about this choice, it depends on what dats structure you use. However, you must implement these options to make them work clearly and consistently.

 

Implementing these options can be problematic in an omnidirectional language like C. Because the dats structure is inactvie. In C, datastructures are controlled by external methods, threads, or processes rather than internal methods.

The idea is to write a module for each major structure that provides standard allocation and deallocation functions for that structure.

## Checking the Resource

Even with this strategy, I am not sure if the resource will be properly freed. Therefore, it is recommended to create code that tracks how the resource is being used.

### resource checking wrapper method

Most applications create wrappers for each type of resource. Make the wrapper track allocation and deallocation. There is a useful point when deciding where to track these cordos. The main program returns to the code at some point after the loop has finished executing. In this case, it is a good option to check if the memory usage has increased after the loop is finished.