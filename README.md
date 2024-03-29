# JNBT - Java Library

[![Maven Build](https://github.com/jackharris-personal/JNBT-Java-Library/actions/workflows/maven.yml/badge.svg?branch=main)](https://github.com/jackharris-personal/JNBT-Java-Library/actions/workflows/maven.yml)

Jack's Named Binary Tree is a specification, format and java module for efficiently saving data to a computer, or sending it over the network! JNBT easily and simply converts its data structure down to the minimal viable bytes to keep file sizes small, whilst providing a robust named map like tree structure to access and manage it programaticly. 

## Getting started using JNBT

You can create tags in any order however unnamed compound tags should be the root structure that nests all other elements.
Looking at the example below we can see us create a CompoundTag called 'root' followed by us creating and adding children to it.
```java
        //Create our root compound tag
        CompoundTag root = new CompoundTag();

        //Add some tags to the root
        root.addChild(new StringTag("date","Monday 4th of March"));
        root.addChild(new ByteTag("year",24));
        
        //Create our nested compound
        CompoundTag root2 = new CompoundTag("jack_harris");
        
        //Add some data tags
        root2.addChild(new StringTag("firstname","Jack"));
        root2.addChild(new ByteTag("age",26));
        root2.addChild(new StringTag("surname","Harris"));
        
        //Add our nested child to the root.
        root.addChild(root2);
```

### Encoding and decoding the root element

When we are done creating our element we can write it to bytes to be then be sent over the network or saved to a data file.
To do this we simply call our write method, this will recursively write all our child elements and parent in the correct order to 
a byte array.
```java
    //Encode root and children to bytes
    byte[] bytes = root.write();

    //Send over network using socket the output stream.
    this.socket.getOutputStream.write(bytes);
    this.socket.getOutputStream.flush();
```

When we need to decode our bytes back into our java objects we can simple pass them into a new instance of a CompoundTag.

```java
    //Decoding from a network input stream.
    byte[] bytes = this.socket.getInputStream().read();
    CompoundTag root =  new CompoundTag(bytes);
```
### Accessing children

Accessing JNBT children is extremely easy, on our root element we can simple call getChild(key) passing it a key that matches
the key of a child element in the root element.

> [!IMPORTANT]
> Given that compounds can be stored recursively in compounds and lists remember to first get the parent element using getChild() followed
> by the child element factoring in the correct depth of your nesting.

```java
    byte year = root.getChild("year").getValue();
```


## Specification

> [!IMPORTANT]
> JNBT (Jack's Named Binary Tree) is not currently cross compatible with NBT (Notchian). Please see the specifications for full details.

JNBT implements the majority of the notichan NBT specification, the first byte of an entry will specify the type, this will
be followed by the key length with the key length being an encoded short value stored in 2 bytes. Following this one of two 
cases will be true, for those tags with fixed length payloads the payload will follow, for those values with dynamic payload sizes the
payload length will follow, followed by the payload itself.

JNBT tags do not require a key and can be key-less (this is forced when an item is stored in a nameless-list) for our fixed payload
length tags we can calculate the total size in bytes with the following function. (you can consider this the minimum total size)

```
    3 + (payload size) = total (bytes)
```


### Fixed size payloads

* ByteTag
* ShortTag
* IntegerTag
* LongTag
* FloatTag
* DoubleTag

### Dynamic size payloads

* ByteArrayTag
* StringTag
* CompoundTag
* IntegerArrayTag
* FloatArrayTag


| ID : 1  (ByteTag)          |        |            |             |         |         |
|----------------------------|--------|------------|-------------|---------|---------|
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 1       |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 2  (ShortTag)         |        |            |            |         |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 2       |
| Field name                 | TAG ID | KEY LENGTH | KEY        | PAYLOAD |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 3  (IntegerTag)       |        |            |            |         |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4       |
| Field name                 | TAG ID | KEY LENGTH | KEY        | PAYLOAD |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 4  (LongTag)          |        |            |            |         |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 8       |
| Field name                 | TAG ID | KEY LENGTH | KEY        | PAYLOAD |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 5  (FloatTag)         |        |            |            |         |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4       |
| Field name                 | TAG ID | KEY LENGTH | KEY        | PAYLOAD |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 6  (DoubleTag)        |        |            |            |         |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 8       |
| Field name                 | TAG ID | KEY LENGTH | KEY        | PAYLOAD |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 7  (ByteArray)        |        |            |             |                |             |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4 (int)        | ? (dynamic) |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD LENGTH | PAYLOAD     |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 8  (StringTag)        |        |            |             |                |             |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 2 (short)      | ? (dynamic) |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD LENGTH | PAYLOAD     |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 9  (ListTag)          |        |            |             |                |             |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4 (int)        | ? (dynamic) |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD LENGTH | PAYLOAD     |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 10  (Compound)        |        |            |             |                |             |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4 (int)        | ? (dynamic) |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD LENGTH | PAYLOAD     |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 11  (IntegerArrayTag) |        |            |             |                |             |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4 (int)        | ? (dynamic) |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD LENGTH | PAYLOAD     |
| -------------------        |--------|------------|-------------|----------------|-------------|
| ID : 12  (FloatArrayTag)   |        |            |             |                |             |
| Size (bytes)               | 1      | 2 (short)  | ? (dynamic) | 4 (int)        | ? (dynamic) |
| Field name                 | TAG ID | KEY LENGTH | KEY         | PAYLOAD LENGTH | PAYLOAD     |
