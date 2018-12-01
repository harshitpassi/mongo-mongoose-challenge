FCC Mongo & Mongoose Challenges
===============================

My Notes:

Mongo and Mongoose

* non-relational NoSQL database. All associated data in one record, not stored across tables like SQL.
* Scalable (non-relational DBs are sharded across systems, making it easier to scale at lower cost), flexible, with replication.
* Mongo uses JSON as document storage structure.
* Mongoose.js - npm module for Node - allows directly writing objects for Mongo
* mLab DB mongo URI: `mongodb://<dbuser>:<dbpassword>@ds123454.mlab.com:23454/fcc-challenge`
* connection to db is established with the function `mongoose.connect('<mongo URI>')`
* Mongoose Basics:
  * Schema - each schema maps to a mongo collection.
  * Models - instances of objects/documents. They use Schemas as building blocks.
  ```javascript
  var schema = new mongoose.Schema({
      name: {type: String, required, true},
      ...
  });
  var model = new mongoose.model(modelname, schema);
  ```
  * Model acts as constructor for documents in mongo. Pass an object containing required data and use the save method to save a document in Mongo.
  ```javascript
  var document = new model({
      name: 'Blah'
  });
  document.save( function(err, data){
      if(err) {
          //handle error
      }
      //saved, data saved available in data variable
  });
  ```
  * Many documents can be created at the same time by passing an array of objects to model.create(). Usage is exactly similar to document.save().
  * Searching - model.find() can be used to search the db. Accepts query document as first argument and callback function as second. Returns array of matches.
  ```javascript
  model.find({
      name: 'Blah'
  }, function(err, data){
      if(err) {
          //handle error
      }
      //found, array of matches is in data variable
  })
  ```
    * Similarly, findOne() returns a single matching document, even if there are more than one matches.
    * While saving any document, Mongo assigns a unique alphanumeric _id field.We can search by _id in the same manner as the other find functions using findById().
  * Updating - dedicated model.update() method can update multiple documents, but only sends back status message, no data. If updated data is needed as well:
    * Classic method - find entry/entries, edit the entry, and still inside the callback, call the document.save() or model.create() method.
    ```javascript
    Person.findById({
        _id: personId
    }, function(err, data){
        if(err) {
            done(err);
        }
        data.favoriteFoods.push(foodToAdd);
        data.save(function(err, data){
        if(err) {
            done(err);
        }
        done(null, data);  
        });
    });
    ```
    * New method - findOneAndUpdate() or findByIdAndUpdate() - By default it returns unmodified object, to get the new one, need to pass the `{new:true}` option object as third parameter.
    ```javascript
    Person.findOneAndUpdate({
        name: personName
    }, {
        $set: {
        age: ageToSet
        }
    } ,{
        new: true
    }, function(err, data){
        if (err){
        done(err);
        }
        done(null, data);
    });
    ```
  * Removing - using findOneAndRemove() or findByIdAndRemove() to delete single items, model.remove() removes all documents matching given criteria, it doesn't return the deleted document, but a JSON object containing the result of deletion and number of documents deleted.
  * Chaining Search Queries - in all find methods, if the callback is not passed, the find operation can be chained. The actual search is executed when you chain the method .exec().
  * Find people who like "burrito". Sort them by name, limit the results to two documents, and hide their age. Chain .find(), .sort(), .limit(), .select(), and then .exec(). Pass the done(err, data) callback to exec().
  ```javascript
  Person.find({favoriteFoods: foodToSearch}).sort('name').limit(2).select('name favoriteFoods').exec(done);
  ```
