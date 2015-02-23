# resource-generator
AngularJS Resource Extension

## About
Resource Generator wraps a call to $resource and provides some extra functionality. The provider can be configured with a base API URL, default parameters, extenders, methods and statics.

While $resource is amazing in itself, we wanted to provide a more declarative approach to $resource management. Typically if you wanted more functions out of your resources, you'd have to create a service that instantiated a resource and then managed the resource with methods on that service. Resource Generator creates one stop call for both your $resource and its management.

## Usage
The ResourceGenerator provider takes in 4 arguments:
- `url`: the URL to get this resource from.
- `paramDefaults`: the hash of parameter defaults for this resource, exactly like $resource. Will also take a function that returns an object.
- `children`: child endpoints. Configured like so:
```javascript
  {
    //A name for your child endpoint.
    'name': 'owners', 
    //URL for this endpoint.
    'url': 'carowners/{id}', 
    //Exactly like the parent, will take an object or a function that returns an object.
    'params': {'id':'@id'}, 
    //Object that has as a key a query field on this child and as a value a variable name to fetch from its parent.
    'link': {'car_id', 'id'} 
  }
```
  

A call to Resource Generator has the same 2 first arguments as $resource:
```javascript
//creates a Cars resource and binds the "id" query field to the object's id
var Cars = ResourceGenerator('cars/:id/', {'id':'@id'});
```
As you can expect, a $resource is returned pointing to the cars endpoint and matches the id url query field with the object's id.

### Methods
Methods allow the manipulation of resource instantiated with your resource-generator. Example:
```javascript
var Cars = ResourceGenerator('cars/:id/', {'id':'@id'}).method('paint', function(color){
  this.color = color;
  return this.$save();
}

//then anywhere you want to use the Cars
var prius = new Cars();
prius.paint('blue');
```

### Statics
Statics (static methods) however, work in the non-stantiated ResourceGenerator, like so:
```javascript
var Cars = ResourceGenerator('cars/:id/', {'id':'@id'}).static('getAllRedCars', function(){
  return this.query({color:'red'});
}

//now to get that list of red cars you call
var redCars = Cars.getAllRedCars();
```

### Extends
Extends are simply _both_ a method and a static - as in they work on both instantiated and non-instantiated versions of your resource object.
```javascript
var Cars = ResourceGenerator('cars/:id/', {'id':'@id'}).extend('toggleIgnition', function(car){
  var useCar = (this.id) ? this : car;
  //toggle between on/off
  useCar.on = !useCar.on;
  
  return useCar.$save();
}

//now we can use that method with an instantiated Car
var prius = new Cars({'name':'prius'});
//turn car on
prius.toggleIgnition();
//and turn it back off, using the non-instantiated Cars
Cars.toggleIgnition(prius);
```

  
    
    
