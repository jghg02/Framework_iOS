#Documentación.

##Creando un Framework en _iOS_.

Lo primero que debemos crear es una _Libreria Estatica_, para esto vamos a __File__ > __New Project__ en esta parte seleccionamos __iOS__ y dentro de esta sección seleccionamos __Cocoa Touch Static Library__

[image Framework1]

Luego le damos _next_ y colocamos el __nombre__ que va a tener nuestra libreria.

[image Framework2]

Salvamos nuestro proyecto y seleccionamos el direcctorio donde lo guardaremos. 

Una vez creado, debemos ver algo como esto.
[image Framework3]

Dentro de __Xcode__ seleccionamos el _Target_ de la libreria que hemos creado y nos vamos a _Build Phase_ y agregamos los __Headers__ (Los cuales seran usados por la app donde importaremos nuestra libreria)

Para agregar los __Headers__ vamos al _Menu Editor_ > _Add Build Phase_ > _Add Headers Build Phase_

[image Framework5]

[image Framework6]

En los __Headers__ solo impostaremos los archivos con extension _.h_ (estos deben ser colocados en __Public__)

[image Framework21]
![](https://github.com/jghg02/Framework_iOS/blob/master/img/Framework1.png)



