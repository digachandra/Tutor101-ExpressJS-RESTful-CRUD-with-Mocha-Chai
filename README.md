<img src=".asset/.banner.png" width="100%" />

### Tutorial 101: Testing RESTful CRUD with Mocha Chai

Hi! welcome to Tutorial 101..
in this tutorial we will learn about how to build testing code for RESTful API CRUD with Mocha Chai. This tutorial is extended series of [Tutorial 101: ExpressJS RESTful CRUD with Mongoose](https://github.com/digachandra/Tutor101-ExpressJS-RESTful-CRUD-with-Mongoose "Tutorial 101: ExpressJS RESTful CRUD with Mongoose"). So let's get started!

---
#### NOTE

**It is totally recommended, if we build testing code first before we code another business requirement**

---

#### 1. Installation

Remember we need to clone or duplicate previous repository, then install the required packages

```
npm install
npm install -S chai chai-http
npm install -D mocha
```

#### 2. Build skeleton

create testing js file **.test.js**, in this case we will create **index.test.js**

```
├── config
│   └── database.js
├── controllers
│   └── fruits.js
├── models
│   └── fruits.js
├── routes
│   └── api.js
├── .gitignore
├── index.js
└── index.test.js
```

#### 3. Init index.test.js

```
'use strict'

const chai = require('chai'),
    chaiHttp = require('chai-http'),
    should = chai.should(),
    expect = chai.expect

chai.use(chaiHttp)

describe('Test API Answer', () => {
  var id
  it(': create record', function(done){
    chai.request('http://localhost:8000')
        .post('/answers')
        .send({
          title: 'Title 1'
        })
        .end(function(err, res){
          expect(res).to.have.status(200)
          id = res.body._id;
          done()
        })
  })
  it(': read data', function(done){
    chai.request('http://localhost:8000')
        .get('/answers')
        .end(function(err, res){
          expect(res).to.have.status(200)
          done()
        })
  })
  it(': update record', function(done){
    chai.request('http://localhost:8000')
        .put(`/answers/${id}`)
        .send({
          title: 'Title 1 New'
        })
        .end(function(err, res){
          expect(res).to.have.status(200)
          done()
        })
  })
  it(': delete record', function(done){
    chai.request('http://localhost:8000')
        .delete(`/answers/${id}`)
        .end(function(err, res){
          expect(res).to.have.status(200)
          done()
        })
  })
})
```

#### 4. Init database config (config/database.js)

```
module.exports = {
  'url': 'mongodb://localhost/tutor101_restful_crud'
}
```

#### 5. Init model (models/fruits.js)

```
var mongoose = require('mongoose')
var Schema = mongoose.Schema;

var fruitSchema = mongoose.Schema({
    name: String
});

module.exports= mongoose.model('fruits', fruitSchema)
```

#### 6. Init controller (controllers/fruits.js)

```
const models = require('../models/fruits'),
      _ = require('lodash')

module.exports = {
  createOne:createOne,
  findAll:findAll,
  findOne:findOne,
  updateOne:updateOne,
  deleteOne:deleteOne
}

function createOne(req, res, next){
  models.findOne({
    name:req.body.name
  },(err, record) => {
    if(err) throw err
    if(!_.isEmpty(record)){
      res.status(400).json({error:"Name already exists"})
    } else {
      var record = new models({
        name:req.body.name
      })
      record.save()
      res.status(200).json(record)
    }
  })
}

function findAll(req, res, next){
  models.find({},(err, record) => {
    if(err) throw err
    if(!_.isEmpty(record)){
      res.status(200).json(record)
    } else {
      res.status(204).json({error:"Cannot find any record"})
    }
  })
}

function findOne(req, res, next){
  models.findOne({
    _id:req.params.id
  },(err, record) => {
    if(err) throw err
    if(!_.isEmpty(record)){
      res.status(200).json(record)
    } else {
      res.status(400).json({error:"Record does not exist"})
    }
  })
}

function updateOne(req, res, next){
  models.findOne({
    _id:req.params.id
  },(err, record) => {
    if(err) throw err
    if(record){
      record.name = req.body.name
      record.save((err)=> {
        if(err) throw err
        res.status(200).json(record)
      })
    } else {
      res.status(400).json({error:"Record does not exist"})
    }
  })
}

function deleteOne(req, res, next){
  models.findOne({
    _id:req.params.id
  },(err, record) => {
    if(err) throw err
    if(record){
      record.remove((err)=> {
        if(err) throw err
        res.status(200).json(record)
      })
    } else {
      res.status(400).json({error:"Record does not exist"})
    }
  })
}
```

#### 7. Init route (routes/api.js)

```
const express = require('express')
const router = express.Router()
const fruits = require('../controllers/fruits')

router.get('/fruits/', fruits.findAll)
router.get('/fruits/:id', fruits.findOne)
router.post('/fruits', fruits.createOne)
router.put('/fruits/:id', fruits.updateOne)
router.delete('/fruits/:id', fruits.deleteOne)

module.exports = router
```

#### 8. Start MongoDb

If you do not know how to get started with MongoDB, you can access these following links

1. [MongoDB Documentation](https://docs.mongodb.com/ "MongoDB documentation")
2. [MongoDB Tutorial for Beginners](https://www.tutorialspoint.com/mongodb/index.htm "MongoDB Tutorial for Beginners")

#### 9. Start the engine!

```
node index.js
```

#### 10. Happy testing!

For testing method, we can use [Postman](https://www.getpostman.com/ "Postman Website")
