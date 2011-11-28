---
title: Protocol
layout: default
---

# STF - Scalable, Simple Object Storage

## What is STF ?

STF (whose name derives from a pro-wrestling maneuver) is a distribute object storage which uses HTTP as its frontend protocol.

## Terminology

* Object

    An object is, from the client's perspective, a file or a piece of
    data, which is associated with a particular object URL.
    
    An object MUST belong to a bucket.

* Bucket

    A bucket is a logical group of objects. A bucket must exist prior to storing an object.

* Entity

    An entity represents a physical copy of an object. While an object is a logical representation of a file/data, an object may be (and usually is) associated with multiple entities in order to ensure that the data exists.

    Entities are sometimes referred to as "replicas".

* Dispatcher

    A dispatcher is the client-facing server which clients interact with. A dispatcher will handles client connections, talk to the backend storages, and serve the necessary data.

* Storage

A storage is a device which stores entities. It must be able to communicate with the dispatcher via HTTP.

## Client Protocol

Here we describe the client protocol.

### Create A Bucket

To create a bucket, an HTTP PUT request should be sent.

     PUT /mybucket HTTP/1.0

If the bucket is successfully created, HTTP Status 201 is returned.

If the bucket already exists, HTTP Status 204 is returned.

If the bucket name cannot be parse (or maybe you specified /bucket_name/object_name instead of /bucket_name), then HTTP Status 400 is returned.

If the were any other errors HTTP Status 500 is returned.

### Delete A Bucket

To delete a bucket, an HTTP DELETE request should be sent.

    DELETE /mybucket HTTP/1.0
    ... optional headers ...

* X-STF-Recursive-Delete

    If set, the bucket AND objects related to this bucket are deleted.
    If unspecified, the bucket is deleted but the objects and entities are
    left alone.

Upon successful deletion, HTTP Status 204 is returned.

If the bucket does not exist, HTTP Status 404 is returned.

If the were any other errors HTTP Status 500 is returned.

### Create An Object

To create an object, an HTTP PUT request should be sent.

     PUT /mybucket/foo/bar/baz/myobject.png HTTP/1.0
     Content-Length: 9153
     ... optional headers ....
     
     ... object data ....

The URL must be in the form /**bucket_name**/**object_name**. The object's content is sent in the HTTP request body.

Optional headers may be one of the following:

* X-STF-Replication-Count

    Specifies the number of entities that this object should have.
    
    Note that upon a successful object creation, you're not guaranteed
    to have this many entities in the storage: STF creates a minimal
    number of entities first, then asks the backend worker to create
    the remaining entities.

    To control the number of object being created upon the initial
    object creation, see the X-STF-Consistency header.

* X-STF-Consistency

    Specifies the number of entities that must be created for this request.

    If the dispatcher fails to create this many entities during
    the HTTP request, the entire operation is considered to have failed.

    You may increase the number of replicas as much as you like, but note
    that increasing this number makes each object creation request take
    much longer to complete. It is recommended that you keep this value
    relatively low.

Upon successful object creation, HTTP Status 201 is returned

If the specified bucket does not exist, HTTP Status 500 is returned.

If the object_name could not be parsed, HTTP STatus 400 is returned

### Retrieve An Object

To retrieve an object, a GET request should be sent.

     GET /mybucket/foo/bar/baz/myobject.png HTTP/1.0
     ... optional headers ...

If-Modified-Since headers are respected.

You may also use HEAD if you only want to check if the object exists.

Upon successful retrieval, HTTP Status 200 and the content of the object is returned.

If the specified bucket does not exist, HTTP Status 500 is returned.

### Modify An Existing Object

To modify an object, a POST request should be sent.

    POST /mybucket/foo/bar/baz/myobject.png HTTP/1.0
    ... optional headers ...

Upon successful retrieval, HTTP Status 204 is returned.

If the specified bucket does not exist, HTTP Status 500 is returned.