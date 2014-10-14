#Documentación.

##Creando un Framework en _iOS_.

Lo primero que debemos crear es una _Libreria Estatica_, para esto vamos a __File__ > __New Project__ en esta parte seleccionamos __iOS__ y dentro de esta sección seleccionamos __Cocoa Touch Static Library__

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework1.png)

Luego le damos _next_ y colocamos el __nombre__ que va a tener nuestra libreria.


Salvamos nuestro proyecto y seleccionamos el direcctorio donde lo guardaremos. 

Una vez creado, debemos ver algo como esto.
![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework3.png)

Dentro de __Xcode__ seleccionamos el _Target_ de la libreria que hemos creado y nos vamos a _Build Phase_ y agregamos los __Headers__ (Los cuales seran usados por la app donde importaremos nuestra libreria)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework4.png)

Para agregar los __Headers__ vamos al _Menu Editor_ > _Add Build Phase_ > _Add Headers Build Phase_

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework5.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework6.png)

En los __Headers__ solo impostaremos los archivos con extension _.h_ (estos deben ser colocados en __Public__)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework21.png)




