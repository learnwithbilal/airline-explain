
## we will explain this airline without image(if you need full explain with images contact with me)
> **_NOTE:_** 
> - the context it means this {} inside the function we pass things from him to html page
> - use comments if you have faced a problem 
> - between functions tow breaks
> - we did not link the css file 
### 1- open cmd and start django project (airline)
```cmd
django-admin startproject airline
```
### 2- now enter  airline folder and start app (flights)
```cmd 
cd airline
django-admin startapp flights
```
### 3- inside airline folder open setting.py and install your app <br> go to installed apps and Write  your appname between couts and close it with comma 
```python
 code . # you can use your editor  
'flights' , # do not forget comma
```
### 4- now go to the urls.py and add your app in new path <br> aside from path import include (path, include) <br> now add new path 
```python
path('flights', include("flights.urls"))
```
### 5- now go to app folder and create new file (urls.py) and write inside him <br> 
```python
from django.urls import path #this for import  path
from . import views     # this for calling functions which are inside views.py
```
### 6- go to views file and write your first function 
```python 
def index(request):
    return render(request, 'flights/index.html', {})  # flights/index.html now we will create this file so do not worry about this
```
### 7- now we  will create  folder (templates) in our app and inside him <br> create folder (flights) inside him create three files 
```python
index.html #  our home page
layout.html # this file are base for other files
flight.html  # we will show details about flight
# we will use this files so do not worry 
```
in layout file right html structure 
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>flight</title>
</head>
<body>
    
</body>
</html>
```
and inside body write 
```python 
{% block body %}
{% endblock %}
# we will use this in the other html files

```

### 9- now get back to views file and we will write the functions which we need
```python 
def index(request):
    return render(request, 'flights/index.html', {})
    # this function is to take us to index file where is flights 
    # we will add some things 
def flight(request, flight_id):
    return render(request, 'flights/flight.html' , {})
    # this function is to take us to flight file where is the details about flight
    # flight_id we will pass it in the url 
 def book(request , flight_id):
    pass   
    # we will pass this function for now and explain it later
    

```
### 10- now go to the urls.py which we create it in our app<br> we will calling functions and give them url so write :
```python 
urlpatterns = [
    path('' , views.index , name="index"),
    path('<int:flight_id>' , views.flight , name="flight"),
    path('<int:flight_id/book>', views.book , name="book") ,
]

```
### 11- now the fun part  GetReady With Models 
### 12- we will start with first model 
```python 
class Airport(models.Model):
    code = models.CharField(max_length=64)
    city = models.CharField(max_length=64)
    # code for city and the city 
    
    def __str__(self):
        return f"{self.city} :{self.code}"
    # this function to return result as string not as object in administration page
```
### 13- the second model is 
```python
class Flight(models.Model):
    origin = models.ForeignKey(Airport , on_delete=CASCADE , related_name="dipartured")
    destination = models.ForeignKey(Airport, on_delete=CASCADE , related_name="arrivals")
    duration = models.IntegerField()

    # foreignkey mean relationshep one to one between Flight and Airport 
    # so now the origin own code and city 
    # on_delete = cascade mean if the airport was delete do not delete the origin and  destination
    # import CASDCADE 
    # write 
    ```python 
    from django.db.models.deletion import CASCADE
    ```
    def __str__(self):
        return f"from {self.origin} to {self.destination}"
```
### 14- the third model is 
```python 
class Passengers(models.Model):
    first = models.CharField(max_length=64)    
    last = models.CharField(max_length=64) 
    flights = models.ManyToManyField(Flight, blank=True, related_name="passengers")

    # ManyToManyField mean many to many relationshep between Passengers and Flight so 
    #  passengers conected with Flight by flights  which mean for each passenger have 
    # flight own origin , destination , duration 
    
    def __str__(self):
        return f"{self.first} {self.last}"
```
### 15- now go to admin.py file and write 
```python 
from .models import Flight , Airport ,Passengers 
# to import models
# Register your models here.


admin.site.register(Airport)
admin.site.register(Flight)
admin.site.register(Passengers)


# syntax admin.site.register(ModelName)
```

### 16- now we need to save models so open cmd and write 
```python
python manage.py makemigrations
python manage.py migrate 
```
now we need to create superuser 
```python 
python manage.py createsuperuser 
1- username
2- email
3- password
4- password:again 
```
after that we need to run the server

```python 
python manage.py runserver 
```

### 17- go to your browser and write 
```python 
http://127.0.0.1:8000/admin/
```
### 18- now we are in administration page 
first 
```
click on Airport and add some cities and codes 
click on Flight and add some flights
click on passengers and add some passengers and choose a flight for them
add passengers and do not choose a flight for them 
```
### 19- now we will back to editior and open views.py file  
### 20- first  we will import models 
```python 
from .models import  Flight , Passengers
### we will add a few things to the functions 
```
### second in index function 
#### we will get all flights and show them to user in index.html file which is homepage 
#### so write inside him 
```python 
flights = Flight.objects.all()
```
#### and inside context {}
```python
"flights" : flights 
```
> **_NOTE:_** 
> - the name inside doublecouts we will use it in html file and it named key
> - the name after : this is the variable and it named value  

#### now the index function will become like that 
```python 
def index(request):
    flights = Flight.objects.all()
    return render(request, 'flights/index.html', {
        "flights": flights
    })
    
```
#### 21- go the index.html file and write 
```python
{% extends 'flights/layout.html' %}
 {% block body %}
 <h1>Flights</h1>
 <ul>
     {% for flight in flights %}
     <li> Flight {{ flight.id }}: from {{ flight.origin }} to {{ flight.destination }}</li>
     {% endfor %}
 </ul>
 {% endblock  %}

```
> **_NOTE:_** 
> - our server is still running if you hava close it run it again
> - and check if there any error 
### 22- back to viwes.py file and in flight function write 
```python 
  flight = Flight.objects.get(id=flight_id)
    # we will get the flight_id in index.html file 
    # we will make url take us to flight page and
    # pass flight_id aside her like that
    # {% url 'flight' flight_id %}
    # and we have all flight details because we pass it in index function in context
    passengers = flight.passengers.all()
    # this passengers var mean the passengers who traveling on this flight 
    # and we get this passengers from Flight model not from Passengers model
```
#### and add this too 
#### take it easy and ask me if was any question 
```python 
non_passengers = Passengers.objects.exclude(flights=flight).all()
    # non_passengers mean that the passengers who don't traveling on this flight 
    # we call Psassengers model and exlude from him passengers who traveling on this flight 
    # we use flights variable  where he exist in Passengers model and conect with Flight model 
    # by ManyToManyField relation shep  and .all() mean all non_passengers
    #-------------------------------------------------------------------
    # i think there is another way we can get passengers var  and 
    # get all passengers from Passengers model like that 
    # passengers = flight.passengers.all() 
    # passengers_all = Passengers.objects.all()
    # non_passengers = passengers_all.exclude(passengers)
```
#### now in the context in flight function write 
```python
       "flight" : flight,
       "passengers": passengers,
       "non_passengers": non_passengers
```
### 23- Go to the flight.html file and write 
```python
{% extends 'flights/layout.html'%}
{% block body %}
<h1> Flight : {{  flight.id }}</h1>
<ul>
<li> Origin : {{ flight.origin}}</li>
<li> Destination : {{ flight.destination}}</li>
<li> Duration : {{ flight.duration }} </li>
</ul> 

<h1>Passengers </h1>
<ul> 
{% for passenger in passengers %}
<li>{{ passenger }}</li>
{% empty %}
 <li> There are no passengers </li>
{% endfor %}
{% comment %} 
empty mean if there are no passengers
 {% endcomment %}


 {% endblock %}
``` 
> **_NOTE:_** 
> - flight_id we will put a value 
> - flight.id we will get a value    

### 23-now we will pass flight_id in url 
#### go to index.html file in list put inside him <a> just like that 
```python 
       <li><a href="{% url 'flight' flight.id %}"> Flight {{ flight.id }}:</a> from {{ flight.origin }} to {{ flight.destination }}</li>
       # all url names we add them first in urls file 
```
### 24- now we will create a form for the passengers who we need to add them 
### go th flight.html file and add this
```python 
   <form method="post" action="{% url 'book' flight.id %}">
     
    {% csrf_token %}
     
   <!-- csrf for token-->
     
      <select name="passenger"> 
        
      # i will explain the name in select 
        
      {% for passenger in non_passengers %}
        
        <option value="{{ passenger.id }}">{{ passenger }}</option>
        
      {% endfor %}
      </select> 
      <input type="submit" value="add">
   </form> 
```
### 25- now go to views.py  
#### first import this 
```python 
  from django.http import HttpResponseRedirect
  from django.urls import reverse
```
``` now add to book function 
```python 
  if request.method == "POST":
        flight = Flight.objects.get(pk=flight_id)

        passenger_id = int(request.POST["passenger"])
        # this is the select name and he get value from 
        # option which is passenger_id 
        # we get the value as int because the default is get her as srting 
  
        passenger = Passengers.objects.get(pk=passenger_id)
        # get the passenger by id

        passenger.flights.add(flight)
        # flights the variable which conect with Flight model ManyToMany
       

        return HttpResponseRedirect(reverse("flight", args=(flight_id, )))
        # flight is url name 
        # we redirect page to flight page which we already inside her and 
        # pass to her by args flight_id to keep us in same flight 
        # we do this for refresh the page and add the new passengers
   return render(request, "flights/flight.html", {})
  
  
### 26- Add simple url to take us from flight to all flights page
#### go to flight.html file and add 
  ```python 
  <a href="{% url 'index' %}">All Flights</a>
```  
  
  
  
  
  
  
  


