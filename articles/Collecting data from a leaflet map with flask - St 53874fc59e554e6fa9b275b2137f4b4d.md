# Collecting data from a leaflet map with flask - Stack Overflow

Created: Feb 27, 2020 2:27 PM
URL: https://stackoverflow.com/questions/52172010/collecting-data-from-a-leaflet-map-with-flask

![apple-touch-icon@2.png](Collecting%20data%20from%20a%20leaflet%20map%20with%20flask%20-%20St%2053874fc59e554e6fa9b275b2137f4b4d/apple-touch-icon2.png)

I'm trying to create a webpage with flask. The webpage includes a leaflet map where I can click on the map to create a marker which opens a popup window with a link. The link is supposed to open a new page where I can see the longitude and latitude. I'm currently struggeling on how to send the leaflet coordinates from my js to flask and then to the second route. Can someone explain to me what I'm doing wrong?

Python file:

```
from flask import Flask, render_template, url_for, request, redirect

app = Flask(__name__)

@app.route('/', methods=["GET","POST"])
def mainpage():

  if request.method == "POST":
      longitude = request.form["longitude"]
      latitude = request.form["latitude"]

      return lredirect(url_for("form", longitude=longitude, latitude=latitude))

  return render_template("main.html")

@app.route('/form')
def form():

  return render_template("form.html")

if __name__ == "__main__":
  app.run(debug=True)
```

main.html file:

```
<!DOCTYPE HTML>
<html lang="en-US">
<head>  

    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Leaflet  CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.3.4/dist/leaflet.css" integrity="sha512-puBpdR0798OZvTTbP4A8Ix/l+A4dHDD0DGqYW6RQ+9jxkRFclaxxQb/SJAWZfWAkuyeQUytO7+7N4QKrDh+drA==" crossorigin=""/>

    <!-- java script -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>

     <!-- Make sure you put this AFTER Leaflet's CSS -->
    <script src="https://unpkg.com/leaflet@1.3.4/dist/leaflet.js" integrity="sha512-nMMmRyTVoLYqjP9hrbed9S+FzjZHW5gY1TWCHA5ckwXZBadntCNs8kEqAWdrb9O7rxbCaA4lKTIWjDXZxflOcA==" crossorigin=""></script>

</head>
<body>

    <div id="mapid" style=" position: fixed;
                            top: 54px;
                            left: 0;
                            bottom: 40px;
                            right: 0;
                            overflow: auto;"></div>

    <script type=text/javascript>

        var mymap = L.map('mapid').locate({setView: true, maxZoom: 16});
        L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
        attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
        id: 'mapbox.streets',
        accessToken: 'your.mapbox.access.token'}).addTo(mymap);

        mymap.on('click', 
            function(e){
                var coord = e.latlng.toString().split(',');
                var lat = coord[0].split('(');
                var lng = coord[1].split(')');
                var test=5;
                var newMarker = L.marker(e.latlng, {draggable:'true'}).addTo(mymap)
                .bindPopup('Create an entry <a href="{{ url_for('form') }}"> here</a>').openPopup();

                $.post( "/", {
                longitude: lng,
                latitude: lat

                });
        });

    </script>

</body>
</html>
```

form.html file

```
<body>
<div class="form">
  <form action="/action_page.php">
    <label for="longi">Longitude</label>
    <input type="text" id="longi" name="longitude" value={{ longitude }}> 
    <label for="lati">Latitude</label>
    <input type="text" id="lati" name="latitude" value={{ latitude }}>
    <input type="submit" value="Submit">
  </form>
</div>
</body>
```