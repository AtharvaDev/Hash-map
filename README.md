# Hash-map
All of the functionality of a Hash Map


Creating the Hash Map Class
Hash maps are efficient key-value stores. They are capable of assigning and retrieving data in the fastest way possible for a data structure. This is because the underlying data structure that they use is an array. A value is stored at an array index determined by plugging the key into a hash function.

In Python we don’t have an array data structure that uses a contiguous block of memory. We are going to simulate an array by creating a list and keeping track of the size of the list with an additional integer variable. This will allow us to design something that resembles a hash map. This is somewhat elaborate for the actual storage of a key-value pair, but it helps to remember that the purpose of this lesson is to gain a deeper understanding of the structure as it is constructed. For real-world use cases in which a key-value store is needed, Python offers a built-in hash table implementation with dictionaries.

Instructions
1.
Create a class called HashMap.


Stuck? Get a hint
2.
Give HashMap a constructor which takes both self and array_size as parameters. array_size should be assigned to an instance variable of the same name (.array_size), and represents the size of the array.


Stuck? Get a hint
3.
Create an instance variable called .array, which is a list of size array_size. Make each element of .array equal to None.



#############################################################################################################
Creating the Hashing Function
The necessary ingredient in the hash map recipe is the hashing function. A hashing function takes a key and returns an index into the underlying array.

Hash functions need to be fast to compute so that access and retrieval can be done fast.

1.
Create a method for HashMap called .hash(). This method should take two arguments: self and key.

2.
Turn the key into a list of bytes by calling key.encode(). Save this into a variable called key_bytes.

.encode() is a string method that converts a string into its corresponding bytes, a list-like object with the numerical representation of each character in the string.

3.
Turn the bytes object into a hash code by calling sum() on key_bytes. Save the result from that into a variable called hash_code.

4.
Return hash_code.



#############################################################################################################

Creating the Compression Function
Hashing functions return a wide range of integers. In order to transform these values into useful indices for our array we need a compression function. A compression function uses modular arithmetic to calculate an array index for a hash map when given a hash code.

Instructions
1.
Create a .compressor() method for your hash map.

It should take two parameters: self and hash_code.

2.
Take the modulus of the hash code by the map’s array_size in order to reduce the hash code to a possible index for the array.

Return the modulus.



#############################################################################################################

Defining the Setter
A data structure that is unable to contain data is a sad sight indeed. We need to put together all the other steps we’ve taken: plug the key into the hash function, plug the hash code into the compression function, use the array index to find the place in the array, and finally set the value of the array to the value we want.

Instructions
1.
Create a .assign() method for the hash map. It should take three parameters: self, key, and value.

2.
Save the value (just the value for now) to the map’s array at the index determined by plugging the key into the .hash() method and plugging the hash code into the .compressor() method.

Hint
Tie all the methods together!

index = self.compressor(self.hash(key))

#############################################################################################################

Defining the Getter
There is a natural expectation after placing an item into a bag that we will later be able to remove the item from that bag. Otherwise we have created a hole. Let’s implement retrieval for our hash map.

Instructions
1.
Define a .retrieve()method for HashMap. It should take two parameters: self and key.

2.
.retrieve() should calculate the array index in the same way our .assign() does and then retrieve the value at that index.

Return that value.

#############################################################################################################

Creating an Instance
Since we have the basic functionality of a hash map, let’s create a test instance of one for us to make sure everything works as expected.

Instructions
1.
Outside the HashMap class (completely unindented below the class definition) create a new hash map called hash_map. Give it an array size of 20.

2.
We want to use this hash map to store geologic information — types of rocks.

In hash_map save the value "metamorphic" for the key "gneiss".

3.
Now retrieve the value of hash_map for the key gneiss. Print it out, does your HashMap work as expected?

#############################################################################################################

Handling Collisions in the Setter
Our hash and compression functions together can result in collisions. This is when two different keys resolve to the same array index. In our current implementation, all keys that resolve to the same index are treated as if they are the same key.

Our first step in implementing a collision strategy is updating our .assign() and .retrieve() methods to set the value with the key and check the key before retrieving a value.

Instructions
1.
We’re going to overwrite the functionality for .assign(). After finding the array_index, we want to do a check of the content that’s currently at self.array[array_index].

In order to avoid overwriting the wrong key, check the existing value in the array at self.array[array_index]. Save this into current_array_value.

2.
There are three possibilities for current_array_value:

It has the same key as key.
It has a different key than key.
It’s None.
If current_array_value already has contents, check if the saved key is different from the key we are currently processing. If the keys are the same, overwrite the array value.

If the keys are different, we’re going to implement a way to find the next array index where our key should go. We’ll get to handling different keys later.

3.
Lastly, we want to store the key and the value instead of just the key if current_array_value is equal to None. Instead of just saving value, save [key, value] to the array.

#############################################################################################################

Handling Collisions in the Getter
When we retrieve hash map values, we also need to be aware of the fact that two keys could point to the same array index.

Instructions
1.
In our .retrieve() method, after finding the array index, we want to check to make sure that the index corresponds to the key we’re looking for.

Save the array value at our compressed hash code into possible_return_value.

2.
Instead of just returning the array’s contents at that index, check if possible_return_value is None. If so, return None.

3.
If possible_return_value is not None, check if the first element in possible_return_value (index 0) is the same as key.

If so, return possible_return_value[1], the value.

4.
If our current array value doesn’t contain the key we’re getting, we’ll need to use open addressing to find the next place where the key will be. We’ll be doing that soon!

#############################################################################################################

Open Addressing
Now we’re going to implement an open addressing system so our hash map can resolve collisions. In open addressing systems, we check the array at the address given by our hashing function. One of three things can happen:

The address points to an empty cell.
The cell holds a value for the key we are getting/setting
The cell holds a value for a different key.
In the first case, this means that the hash map does not have a value for the key and no collision resolution needs to happen. Notice that this does not work if we want to be able to delete keys in our hash map. There are strategies for deleting pairs from a hash map (see Lazy Deletion) but we will not be investigating these.

In the second case, we’ve found the value for our key-value pair!

In the third case, we need to use our collision addressing strategy to find if our key is somewhere else (it may or may not be) so we should recalculate the index of our array.

Instructions
1.
Give HashMap.hash() another parameter: count_collisions. This will be the number of times the .hash() has hit a collision.

Have count_collisions default to 0.


Hint
Give a function a default argument by using an equals sign in the function definition:

def cool_function(default=1):
  return default
2.
Instead of returning hash_code from .hash(), return hash_code + count_collisions.


#############################################################################################################

Open Addressing in the Setter
Now lets use our open addressing scheme in the setter for our HashMap.

Instructions
1.
Now that we have a hash function that uses the number of collisions to determine the hash code, we can update where we set a key in the event of a collision.

When we notice that the key we’re trying to set is different from the key at our hash code’s address, create a new variable called number_collisions, set that equal to 1.

2.
After defining number_collisions, create a new while loop that checks if current_array_value[0] != key.

3.
In the while loop, you want to replicate our setting logic while incrementing the number of collisions.

Call .hash() with both the key and number_collisions. Save that result into new_hash_code.

4.
Plug new_hash_code into .compressor(). Save that result into new_array_index.

5.
Check self.array at new_array_index and save the result as current_array_value. Check against the three possibilities:

If it’s None, save the [key, value] at self.array[new_array_index] and then return.
If it has a value, but the same key as key, overwrite the array at that address with [key, value] and then return.
If it has a value, but a different key, increment number_collisions.

#############################################################################################################

Open Addressing in the Getter
With everything in our setter taken care of, we want to make sure that when we retrieve our value we’re retrieving the correct value.

Instructions
1.
In .retrieve() if possible_return_value has a different key than the one we’re looking for, we should continue searching.

Define a new variable called retrieval_collisions and set it equal to 1.


Stuck? Get a hint
2.
Insert a new while loop that checks if

possible_return_value[0] != key
In the while loop, we want to replicate our retrieval logic while increasing the count of retrieval_collisions so that we continue to look at other locations within our array.

Call .hash() with both the key and retrieval_collisions. Save that result into new_hash_code.

3.
Plug new_hash_code into .compressor(). Save that result into retrieving_array_index.

4.
Check self.array at retrieving_array_index and save the result as possible_return_value. Check against the three possibilities:

If it’s None, return None
If it has a value, but a different key, increment retrieval_collisions.
If it’s key matches our key return possible_return_value[1].

#############################################################################################################

Review
Now that we have all of the functionality of a Hash Map, it’s time to review what we’ve learned!

Instructions
1.
Create a hash map hash_map. Give it an array size over 15.

2.
Assign the following key-value pairs to hash_map:

Key: 'gabbro', Value: 'igneous'
Key: 'sandstone', Value: 'sedimentary'
Key: 'gneiss', Value: 'metamorphic'
3.
Retrieve the value from each of the three keys from hash_map. Print them out!

4.
Congratulations! You implemented a fully-functional hash map! Some things to consider:

How would you delete a key-value pair from this hash map?
Parts of the code are a little repetitive, how would you factor these roles differently?
What should your hash map do if a key-value is added and the array is full? How does this hash map handle that?

#############################################################################################################



