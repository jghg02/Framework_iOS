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

En los __Headers__ solo importaremos los archivos con extension _.h_ (estos deben ser colocados en __Public__)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework21.png)

Luego de agregar los _Headers_ es necesario agregar el __script__ para de esta forma cada vez que hagamos el __build__ de la libreria genere queleto de nuestro framework. 

Para esto vamos al _Menu Editor_ > _Add Build Phase_ > _Add Run Script Build Phase_ 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework7.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework8.png)

###Script

	set -e

    mkdir -p "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Versions/A/Headers"

     # Link the "Current" version to "A"
     /bin/ln -sfh A "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Versions/Current"
    /bin/ln -sfh Versions/Current/Headers "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Headers"
    /bin/ln -sfh "Versions/Current/${PRODUCT_NAME}" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/${PRODUCT_NAME}"

    # The -a ensures that the headers maintain the source modification date so that we don't constantly
    # cause propagating rebuilds of files that import these headers.
    /bin/cp -a "${TARGET_BUILD_DIR}/${PUBLIC_HEADERS_FOLDER_PATH}/" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.framework/Versions/A/Headers"







