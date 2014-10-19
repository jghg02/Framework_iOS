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



###Creando el Framework Distribution Target.

Para esto vamos al Menu __File__ > __Target__ > __iOS__ > __Other__  and __create a new agregate__

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework9.png)

Seleccionamos __Target__

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework10.png)

Y colocamos el nombre del _Target_ y seleccionamos el proyecto con el cual lo vamos a asociar.

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework11.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework12.png)

Luego agregamos en el __Build Phase__ el _script_. Para esto vamos al menu __Editor__ > __Add Build Phase__ > __Add Run Script Build Phase__ 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework13.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework14.png)

###Script 

    set -e
    set +u
    # Avoid recursively calling this script.
    if [[ $SF_MASTER_SCRIPT_RUNNING ]]
    then
     exit 0
    fi
    set -u
    export SF_MASTER_SCRIPT_RUNNING=1

    SF_TARGET_NAME=${PROJECT_NAME}
    SF_EXECUTABLE_PATH="lib${SF_TARGET_NAME}.a"
    SF_WRAPPER_NAME="${SF_TARGET_NAME}.framework"

    # The following conditionals come from
    # https://github.com/kstenerud/iOS-Universal-Framework

     if [[ "$SDK_NAME" =~ ([A-Za-z]+) ]]
     then
        SF_SDK_PLATFORM=${BASH_REMATCH[1]}
     else
        echo "Could not find platform name from SDK_NAME: $SDK_NAME"
        exit 1
    fi

    if [[ "$SDK_NAME" =~ ([0-9]+.*$) ]]
    then
       SF_SDK_VERSION=${BASH_REMATCH[1]}
    else
      echo "Could not find sdk version from SDK_NAME: $SDK_NAME"
      exit 1
    fi

    if [[ "$SF_SDK_PLATFORM" = "iphoneos" ]]
    then
       SF_OTHER_PLATFORM=iphonesimulator
    else
       SF_OTHER_PLATFORM=iphoneos
    fi

    if [[ "$BUILT_PRODUCTS_DIR" =~ (.*)$SF_SDK_PLATFORM$ ]]
    then
       SF_OTHER_BUILT_PRODUCTS_DIR="${BASH_REMATCH[1]}${SF_OTHER_PLATFORM}"
    else
       echo "Could not find platform name from build products directory:        $BUILT_PRODUCTS_DIR"
       exit 1
    fi

    # Build the other platform.
    xcrun xcodebuild -project "${PROJECT_FILE_PATH}" -target "${TARGET_NAME}" -configuration "${CONFIGURATION}" -sdk ${SF_OTHER_PLATFORM}${SF_SDK_VERSION} BUILD_DIR="${BUILD_DIR}" OBJROOT="${OBJROOT}" BUILD_ROOT="${BUILD_ROOT}" SYMROOT="${SYMROOT}" $ACTION

     # Smash the two static libraries into one fat binary and store it in the .framework
     xcrun lipo -create "${BUILT_PRODUCTS_DIR}/${SF_EXECUTABLE_PATH}" "${SF_OTHER_BUILT_PRODUCTS_DIR}/${SF_EXECUTABLE_PATH}" -output "${BUILT_PRODUCTS_DIR}/${SF_WRAPPER_NAME}/Versions/A/${SF_TARGET_NAME}"

     # Copy the binary to the other architecture folder to have a complete framework in both.
     cp -a "${BUILT_PRODUCTS_DIR}/${SF_WRAPPER_NAME}/Versions/A/${SF_TARGET_NAME}" "${SF_OTHER_BUILT_PRODUCTS_DIR}/${SF_WRAPPER_NAME}/Versions/A/${SF_TARGET_NAME}"


Seleccionado el _Target_ que hemos creado en la sección de __Build Phase__ -> __Target Dependencies__ y agregamos la libreria que creamos.

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework15.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework16.png)

Luego creamos el __Target__ para esto vamos al menu __File__ > __New__ > __Target__ > __OS X__ > __Bundle__ 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework17.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework18.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework19.png)

Procedemos a cambiar el __Base SDK__ 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Framework20.png)

Por ultimo creamos el _Build_ de la __Libreria__ y el __Target__ 


###Project App.

Procedemos a crear un nuevo proyecto __iOS__ > __Application__ > __Single View App__ Colocamos el nombre y un directorio donde se almacenara el nuevo proyecto.

Para poder integrar nuestra nueva app y el _framewrok_ que hemos creado, es necesario crear un __WorkSpace__ 


![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work1.png)
 
 
![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work2.png)

Vamos al directorio donde creamos los proyectos y los arrastramos al __WorkSpace__ tanto la libreria como nuestra app que hemos creado.

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work3.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work4.png)

En nuestro proyecto (__TestApp__) creamos una carpeta llamada _Libraries_ y arrastramos la libreria __Testing__ (que creamos).

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work5.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work6.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work7.png)

Compilamos el __Target__ 

Procedemos a agregar la libreria a nuestro proyecto. Para esto vamos al __Target__ de nuestra app y en la seccion de __Build Phase__ > __Target Dependencies__ y agregamos el _Bundle_ y el _Target_ que hemos creado anteriormente. 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work8.png)

Por ultimo creamos una carpeta llamada __framework__ y copiamos los archivos __.framework__ y __.bundle__ los cuales son generadmos al momento de compilar nuestra libreria. 

Para esto vamos a la carpeta __Products__ de la libreria que hemos creado y damos click derecho cobre y ella y seleccionamos __Show in Finder__ 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work9.png)

Y se vera algo como esto.

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work10.png)

Luego a la carpeta _framework_ que creamos arrastramos el archivo con extension __.bundle__ y __.framework__ 

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work11.png)

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work12.png)

Ya con esto podemos realizar el _import_ en nuestro proyecto.

![](https://raw.githubusercontent.com/jghg02/Framework_iOS/master/img/Work13.png)






