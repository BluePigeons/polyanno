# polyanno
Package to develop transcription and translations of images!

##Setup Options

To get started you need to run **polyanno_setup** function.
```
polyanno_setup(opts);
```

The function that takes one input, *opts*,  a JSON object of the following format :
```
{
  "highlighting": Boolean,
  "minimising": Boolean,
  "voting": Boolean,
  "users": Object,
  "storage": Object
}
```

###highlighting

Type: Boolean
Default: False

If false then this disables the highlighting functionality that enables any annotation targets present on the page to be 'highlighted' whenever a cursor hovers over an annotation.

The default, non-highlighted colours of objects can be defined by setting the variable **polyanno_default_colours_array** before running the setup. This is an array of three HTML colours in the format [editor_box, vector, span]. The default values are:
```
//[editor_box, vector, span]
polyanno_default_colours_array = ["buttonface","#03f","transparent"]; 
```

The highlighted colours that the objects change to are similarly defined by **polyanno_highlight_colours_array** with the defaults as follows:
```
//[editor_box, vector, span]
polyanno_highlight_colours_array = ["#EC0028","#EC0028","#EC0028"];
```

###minimising

Type: Boolean
Default: True

If false then the editor boxes displaying the annotations and the image cannot be minimised, only opened and closed.

###voting

Type: Boolean
Default: True

If false then users cannot vote annotations up using Polyanno voting structure - see [here](#) for more info on voting.

###users (Optional)

Type: JSON object
Default: no users implemented and therefore the **favourite** functionality of Polyanno is disabled.

The **users** field of the setup options takes the following format:

```
{
  "favourites": Boolean,
  "users_url": String
}
```

This assumes that if you GET the **users_url** URL with "/username/" and a specific username, or "/id/" and specific id number, then it will recieve the user info in a JSON format. 
So for example, if **users_url** is "www.example.com/users" then Polyanno can send a GET request to "www.example.com/users/username/user_one" to recieve user_one's info as JSON.

Similarly if you PUT the **users_url** URL with the specific username added to the end and the fields to be updated as body parameters then it can be updated. 
So for example, if *users_url** is also "www.example.com/users" then Polyanno can send a PUT request to "www.example.com/users/user_one" to update user_one's info.

It also requires that the user info has at least the following fields in the following format:
```
{

  "username": String,

  "docs_edited": {
    "vectors" : {
      "created" : [],
      "edited" : [],
      "deleted" : []
    },
    "transcriptions" : {
      "created" : [],
      "edited" : [],
      "deleted" : []
    },
    "translations" : {
      "created" : [],
      "edited" : [],
      "deleted" : []
    }
  },

  "favourites" : [{
    "image_id" : String,
    "the_image" : {
      "type": Boolean,
      "default": false
    },
    "transcriptions" : [],
    "translations" : [],
    "vectors" : []
  }]
}
```
###storage

Type: JSON object
Default: the **base_url** is set to the current host address of the web page running.

The **storage field** takes format:
{
  "base_url": String,
  "transcription": String,
  "translation": String,
  "vector": String,
  "annotation": String
}

If nothing is specified other than base_url then it simply assumes base_url + "/transcriptions", "/translations", "/vectors", and "/annotations".
The storage assumes you are using [polyanno_storage](https://github.com/BluePigeons/polyanno_storage) and so the request mapping to function is assumed to be:

####ANNOTATIONS

POST +'/annotations' --> polyanno_storage.annotations.addNew 
PUT +'/' --> polyanno_storage.annotations.updateOne 
GET +'/annotations' --> polyanno_storage.annotations.getAll
GET +'/annotations/vectors' --> polyanno_storage.annotations.getAllVectorAnnos
GET +'/annotations/transcriptions' --> polyanno_storage.annotations.getAllTranscriptionAnnos
GET +'/annotations/translations' --> polyanno_storage.annotations.getAllTranslationAnnos
GET +'/annotations/:anno_id' --> polyanno_storage.annotations.getByID
GET +'/annotations/target/:target_id' --> polyanno_storage.annotations.getByTarget

####VECTOR

GET +'/vectors' --> polyanno_storage.vectors.findAll
POST +'/vectors' --> polyanno_storage.vectors.addNew
GET +'/vectors/:vector_id' --> polyanno_storage.vectors.getByID
PUT +'/vectors/:vector_id' --> polyanno_storage.vectors.updateOne
DELETE +'/vectors/:vector_id' --> polyanno_storage.vectors.deleteOne
DELETE +'/vectors' --> polyanno_storage.vectors.deleteAll

GET +'/vectors/targets/:target' --> polyanno_storage.annotations.getVectorsByTarget
GET +'/vectors/ids/:_ids' --> polyanno_storage.vectors.searchByIds

####TRANSCRIPTION 

GET +'/transcriptions' --> polyanno_storage.transcriptions.findAll
POST +'/transcriptions' --> polyanno_storage.transcriptions.addNew
GET +'/transcriptions/:transcription_id' --> polyanno_storage.transcriptions.getByID
PUT +'/transcriptions/:transcription_id' --> polyanno_storage.transcriptions.updateOne
PUT +'/transcriptions/voting/:voteType' --> polyanno_storage.transcriptions.voting
DELETE +'/transcriptions/:transcription_id' --> polyanno_storage.transcriptions.deleteOne
DELETE +'/transcriptions' --> polyanno_storage.transcriptions.deleteAll

GET +'/transcriptions/targets/:target' --> polyanno_storage.annotations.getTranscriptionsByTarget
GET +'/transcriptions/ids/:_ids' --> polyanno_storage.transcriptions.searchByIds

####TRANSLATION 

GET +'/translations' --> polyanno_storage.translations.findAll
POST +'/translations' --> polyanno_storage.translations.addNew
GET +'/translations/:transcription_id' --> polyanno_storage.translations.getByID
PUT +'/translations/:transcription_id' --> polyanno_storage.translations.updateOne
PUT +'/translations/voting/:voteType' --> polyanno_storage.translations.voting
DELETE +'/translations/:transcription_id' --> polyanno_storage.translations.deleteOne
DELETE +'/translations' --> polyanno_storage.translations.deleteAll

GET +'/translations/targets/:target' --> polyanno_storage.annotations.getTranslationsByTarget
GET +'/translations/ids/:_ids' --> polyanno_storage.translations.searchByIds


