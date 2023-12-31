---
title: Web Scraping utilizando 'Selenium'
collection: lessons
layout: lesson
slug:
authors:
- Jose Hernández Pérez
editors:
-
reviewers:
date: 07/10/2023
difficulty: 
activity: acquire
topics: 
abstract: En esta lección aprenderás a utilizar la herramienta Webdriver que es parte de Selenium para extraer datos de una página web
avatar_alt:
doi: 
review-ticket:
---
{% include toc.html %}

## Introduccion y Objetivos

En esta leccion aprenderemos a implementar el paquete de Python _selenium_ para automatizar nuestro acceso a páginas web y la extraccion de datos. Al final de esta lección usted va a poder

- utilizar la función de Webdriver del paquete Selenium
- Interactuar con una página web de una manera automatizada
- Exportar los datos adquiridos para futuro uso en su analisis en formato CSV (Comma separated values - formato 'default' de Microsoft Excel)

Para llevar a cabo nuestro análisis estaremos analizando la página <a href="https://lp.espacenet.com/?locale=es_LP">Latipat</a> la cual es una colección de patentes a través de los países iberoamericanos. En esta página someteremos una búsqueda para patentes que tengan que ver con la industria agrícola y vamos a extraer los títulos y resúmenes de las primeras 10 patentes de la búsqueda a un documento CSV para futuro uso.

## Pre-requisitos

Para utilizar esta lección no se necesita conocimiento extensivo en el lenguaje de programación Python, pero se recomienda que el lector tenga alguna experiencia en el lenguaje. Un conocimiento de la estructura e implementación básica, le permitirá al usuario editar fácilmente el código de ejemplo en esta lección para sus futuros proyectos.

También se recomienda que el lector se familiarice con XPATH que es la forma de navegar documentos HTML en páginas web para encontrar los elementos deseados para la extracción de datos.

## ¿Que es Web-Scraping o el raspado web?

Cuando hablamos del raspado web nos referimos a la extracción de datos del Internet. Usualmente esta extracción es automatizada y resulta en una colección de datos con toda la información extraída organizada de alguna manera coherente. En nuestro mundo digital hay múltiples herramientas que se dedican a facilitar el raspado web que suelen automatizar todo el proceso desde acceder a la página deseada hasta copiar la información a una base de datos local. Una de estas herramientas que automatiza todo el proceso de principio a fin es _Selenium_.

## Selenium: una introducción

Selenium es una colección de herramientas para automatizar navegadores web. Dentro de esta amplia definición, Selenium ofrece apoyo para múltiples navegadores web y lenguajes de programación. Debido a la facilidad de aprendizaje, una de sus implementaciones más famosas es la biblioteca Webdriver en Python la cual permite al usuario automatizar el acceso a una página y navegarla a través de código y no una interfaz. Una interfaz es la manera usual de interactuar con una página web utilizando su cursor y el teclado.

Es importante aclarar que para muchos proyectos no se necesita usar selenium ya que existen herramientas más fáciles para extraer data. Por ejemplo muchos sitios web hoy ofrecen la forma de descargar su data en distintos formatos y muchos de los que no tiene este acceso fácil permite el uso de API ( Application programming interface) la cual muchas veces facilita el acceso a data.

Pero si usted se encuentra con un proyecto que necesita datos de una página que no ofrece un API o la descarga fácil, Selenium y otras herramientas de raspado web le permitirán continuar y cumplir sus metas.

## Preparacion dependiendo el browser

### Instalacion en Python

El primer paso para utilizar Selenium webdriver es instalarlo en su ambiente de Python que usualmente se puede hacer con la siguiente línea en el terminal

```
pip install selenium
```

Para nuestro tutorial también es necesario que se instale el paquete __csv__.

```
pip install csv
```

Después de esas instalaciones la forma de usar estos paquetes en su documento de Python es

```
from selenium import webdriver
import csv

```

En adición a estas dos líneas __selenium__ tiene muchos sub-paquetes que se utilizan en su implementación por lo que se recomienda que estas líneas también se corran al principio del documento para tener acceso a la funcionalidad básica completa de __selenium__

```
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait 
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions as EC
```

Todas estas no son usadas por el tutorial pero le permite la mayor flexibilidad en sus proyectos iniciales. Es importante saber que estamos usando las herramientas para usar selenium con el browser _Chrome_ y estas líneas cambiaran dependiendo del browser que utilice.

### Selección de browser

Como señalado anteriormente las opciones requeridas por su código depende del buscador que usted use para acceder a la página web. Por lo tanto, si usted usa Chrome, Firefox, Edge, Internet explorer, o Safari. En la siguiente sección se explicarán los cambios que cada una de estas opciones lleva, pero estos cambios nada más ocurren en la preparación del código. Una vez esta preparación es terminada todo el código es idéntico, no importa que buscador se use para inicializar el proceso.

### Instalación del webdriver

Una vez usted seleccione que buscador va a utilizar, tiene que descargar el Driver específico (programa que inicializa la búsqueda automatizada) a ese buscador. Por ejemplo, para Chrome el driver especifico se Conoce como Chromedriver y se descarga <a href="https://chromedriver.chromium.org/downloads">aqui</a>

Para otra plataforma como Firefox se conoce como el geckodriver y se descarga <a href="https://github.com/mozilla/geckodriver/releases">aqui</a>

Todos los webdriver que funcionan con Selenium para las demás plataformas se encuetran en la siguiente <a href="https://www.selenium.dev/documentation/webdriver/drivers/">pagina</a>. Por favor mantenga en mente que la versión de su webdriver debe coincidir con la versión de su buscador, ya que estas cambian con frecuencia!!

después de seleccionar su browser y descargar el 'driver' específico tiene que inicializar el driver en su codigo de Python. Si su código y el programa descargado se encuentran en el mismo folder en su sistema la siguiente línea es suficiente 

Chrome:
```
driver = webdriver.Chrome()
```

Firefox: 
```
driver = webdriver.Firefox()
```

Pero se __recomienda fuertemente__ que se detalle el camino completo de donde se encuentra el documento. Por ejemplo: 

Chrome:
```
driver = webdriver.Chrome('/Users/Pepito/Documents/chromedriverfolder/chromedriver')
```

Firefox: 
```
driver = webdriver.Firefox('/Users/Pepito/Documents/geckodriverfolder/geckodriver')
```

¡¡Una vez esta línea corra, _Felicidades!!_ Usted ha inicializado una instancia automatizada para acceder a una página web y está listo para empezar su raspado web.

## Acceso a la página web

Hasta ahora su código en su documento de Python se supone que se vea asi:

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait 
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions as EC
import csv

#Iniciacion del driver- por favor recuerde incluir el nombre del programa final y no solamente el ultimo folder!

driver = webdriver.Chrome('/Users/joseh/Downloads/chromedriverfolder/chromedriver')

```

Reiteramos que nuestro ejemplo solamente utilizara Chrome, pero fácilmente puede hacer lo mismo con los demás browsers.

Ahora añadimos la línea que le ordena al driver a abrir una página especifica en este caso __Latipat__ que usa el URL https://lp.espacenet.com/?locale=es_LP

```
driver.get('https://lp.espacenet.com/?locale=es_LP')
```

Una vez se corra este código el driver abre la página web automáticamente, pero todavía no tiene instrucciones de como interactuar con ella.

Aqui se debe notar también que _'driver.'_ permite acceso a todos los métodos que tiene el driver de selenium; desde interactuar con elementos específicos de la página hasta cerrar nuestro buscador. Por esta razon todas las instrucciones que siguen, excepto las de procesamiento de data, usan _'driver.'_ antes del método.

## Navegación en la Página web

Al abrir nuestra página web, nuestras próximas acciones son casi infinitas ya que podemos hacer todo lo que haríamos normalmente en una página: hacer click en distintos botones, usar la barra de búsqueda, cerrar la página, etc. 

Para el propósito del taller ahora usaremos el buscador para hacer una búsqueda de las patentes que nos interesan en este caso las que tengan que ver con la industria agrícola llenando el buscador con la palabra 'agrícola'

Para llegar a someter la palabra al buscador primero lo tenemos que encontrar y aqui es donde nos resulta útil saber que estas páginas estan organizadas utilizando HTML y por lo tanto tienen una estructura regular y facil de leer. 

#### Buscando un elemento

Si usa su clic derecho encima del elemento de interés en este caso el buscador le debe salir la opción de 'Inspect' o inspeccionar. Si le da clic a esta opción le va a salir un panel usualmente en su mano derecha del Código HTML de la página en cuestión. Este proceso suele variar dependiendo de su buscador pero esta estandarizado en los buscadores prominentes como Chrome, Firefox, y Edge.

![alt imagen de inspect](imagenes/inspect.png)

Panel de inspección:

![alt imagen de panel](imagenes/panel.png)

Aquí usted puede notar que cada elemento en HTML suele tener unos atributos que nos permiten identificar y distinguir distintos elementos fácilmente. Por ejemplo, alugnos usan 'id' y otros usan, como en este caso, 'name'. Por lo tanto, sabemos que el buscador es el único elemento con el nombre query lo cual lo hace facil de buscar con la siguiente línea: 

```
search_box = driver.find_element(By.NAME,'query')
```

En esta línea definimos una variable llamada search_box la cual le dice al driver que busque el elemento (__find_element__) con el nombre 'query (__By.NAME, 'query'__).

Es importante notar que esta no es la unica manera de encontrar un elemento. Por ejemplo el mismo elemento de la caja del search se puede buscar por su camino de XPATH o su camino largo a través de la estructura de HTML con la siguiente linea:

```
search_box= driver.find_element(By.XPATH,'/html/body/div/div[6]/div/div/form/div[1]/span[3]/textarea')
```

Para encontrar este XPATH completo va a copiar el elemento en el mismo panel de la derecha que abrimos cuando buscamos el nombre del atributo 'query'.

Este método se explicara en detalle más adelante cuando se use para la busqueda de datos específicos pero se recomienda que si lo va a usar defina el XPATH como una variable para evitar errores de transcripción.

```
xpath1 = ''/html/body/div/div[6]/div/div/form/div[1]/span[3]/textarea'
search_box= driver.find_element(By.XPATH, xpath1)

```

#### Interactuando con el elemento 

Ya que encontramos la caja de búsqueda ahora la podemos utilizar usando las siguientes líneas: 

```
#Termino para la búsqueda
search_box.send_keys('agricultura')

#Sometemos búsqueda
search_box.send_keys(Keys.RETURN)

```

Ahora su código completo se debe ver así:

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait 
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions as EC
import csv

#Iniciación del driver- por favor recuerde incluir el nombre del programa final y no solamente el ultimo folder!

driver = webdriver.Chrome('/Users/joseh/Downloads/chromedriverfolder/chromedriver')

driver.get('https://lp.espacenet.com/?locale=es_LP')

search_box = driver.find_element(By.NAME,'query')

#Termino para la busqueda
search_box.send_keys('agricultura')

#Sometemos busqueda
search_box.send_keys(Keys.RETURN)
```

Y en vez de la página principal de latipat debe estar viendo esta: (sus resultados pueden ser distintos dependiendo de la fecha en la que corra esta programa)

![alt imagen de resultados de busqueda agricultura](imagenes/resultado.png)

#### Integrando loops con búsqueda de elementos

Las instrucciones anteriores funcionan perfectamente si tenemos un solo elemento de interés como la barra de búsqueda. Pero después de haber buscado los elementos ahora queremos extraer datos que se repite en múltiples instancias.

Ahora vamos a extraer los títulos y los resúmenes de cada patente. Para hacer esto tenemos que hacer el siguiente proceso en tres pasos: 

 - Paso 1: Acceder a la primera patente

 - Paso 2: Copiar la informacion al CSV

 - Paso 3: Movernos a la siguiente patente y repetir el paso 2

##### La primera patente

Para acceder a la primera patente tenemos que verificar su XPATH o camino y asignarle una variable. Esto lo va a hacer con el mismo panel que utilizamos para encontrar el identificador para la barra de búsqueda, pero ahora copiara el XPATH del elemento directamente.

![alt imagen de Copy XPATH primera patente](imagenes/primerapatente.png)

Para realizar esta búsqueda asignamos este XPATH a una variable y después buscamos el elemento con el driver. Finalmente le damos click al link de la patente para entrar a la próxima página, la cual se puede ver en la imagen que sigue.

```
xpath1='//*[@id="publicationId1"]'
publicacion1= driver.find_element(By.XPATH,xpath1)
driver.execute_script("arguments[0].click();", publicacion1)
driver.implicitly_wait(10)
```

Como puede ver aquí tenemos dos métodos nuevos:
 - El primero es __.execute_script()__ el cual le dice al driver que le de click al botón apropiado.
 - El segundo es __.implictly_wait()__ el cual le dice al driver que espere 10 segundos o menos en lo que sube la pagina buscada. Este metodo se usa para websites lentos o para asegurarse que los elementos están presentes en la página antes de extraer cualquier infromacion.

Con este metodo le damos click a el elemento (__piblicacion1__) con el XPATH apropriado ( __xpath1__)

##### Copiando la información al CSV

Ahora identificaremos los elementos que queremos extraer de cada página de la patente (_el título y el resumen_)

![alt imagen de asbtract y titulo](imagenes/resumen.png)

Al identificar los elementos que queremos, tenemos que copiar sus XPATH en la misma manera que lo hicimos para el botón de la primera patente.

![alt imagen panel para resumen](imagenes/panelresumen.png)

Los caminos se pueden identificar de la siguiente manera: 

```
    xpathres='//*[@id="body"]/div[2]/p[1]'
    xpathtitulo='//*[@id="pagebody"]/h3'
```

y para extraer su texto vamos a utilizar el método de __get_attribute__

```
    titulo= driver.find_element(By.XPATH,xpathtitulo).get_attribute('innerText')
    resumen=driver.find_element(By.XPATH,xpathres).get_attribute('innerText')
```

Despues de tener su texto extraído podemos usar las funciones de la biblioteca __csv__ para escribir nuestra data al nuevo documento: 

```
    data=[titulo,resumen]

    with open('output.csv', 'a', encoding='UTF8', newline='') as document:
        writer= csv.writer(document)
        writer.writerow(data)

```

Preste atención que el documento se abrió con el argumento 'a' que significa 'append' o 'añadir'. Si utiliza el argumento 'w' cada línea nueva va a borrar la entrada anterior.

##### Pasando a la siguiente patente

Para darle clic al botón vamos a usar otra función de Selenium que se llama __WebDriverWait__. Esta función le dice al driver que espere hasta que una acción ocurra. En nuestro caso estamos esperando (un máximo de 10 segundos) hasta que el botón _siguiente_ aparezca y le vamos a dar clic con el método __.click__ en vez de la linea de código que usamos antes. Esta línea se ve de la siguiente manera para nuestro boton de _siguiente_: 

```
WebDriverWait(driver,10).until(EC.element_to_be_clickable((By.ID,'nextDocumentLink'))).click()
```

Aquí podemos observar que buscamos el elemento usando su id unico que se puede verificar en el panel de la derecha.

##### Loop final

Uniendo el acceso a la primera patente (que nada más se ejecutará una vez), la extracción de datos y la transferencia a la próxima patente podemos ahora unirlo todo en un __for__ loop en Python. Este loop nos permitirá repetir las acciones la cantidad de veces que queramos para añadir todos los datos extraidos en un solo documento.

```
xpath1='//*[@id="publicationId1"]'
publicacion1= driver.find_element(By.XPATH,xpath1)
driver.execute_script("arguments[0].click();", publicacion1)
driver.implicitly_wait(10)

for i in range(1,16):

    xpathres='//*[@id="body"]/div[2]/p[1]'
    xpathtitulo='//*[@id="pagebody"]/h3'
    titulo= driver.find_element(By.XPATH,xpathtitulo).get_attribute('innerText')
    resumen=driver.find_element(By.XPATH,xpathres).get_attribute('innerText')
   
    data=[titulo,resumen]

    WebDriverWait(driver,10).until(EC.element_to_be_clickable((By.ID,'nextDocumentLink'))).click()

    with open('output.csv', 'a', encoding='UTF8', newline='') as f:
        writer= csv.writer(f)
        writer.writerow(data)

```

Si usted no sabe cómo funciona el loop __for__  en Python por favor lea esta <a href="https://www.freecodecamp.org/espanol/news/bucle-for-en-python-ejemplo-de-for-i-en-range/">página web</a>

## Codigo final 

Ya hemos cubierto todos los elementos por separado así que nuestro código final se vería de la siguiente manera:

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait 
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions as EC
import csv

#Iniciación del driver- por favor recuerde incluir el nombre del programa final y no solamente el último folder!

driver = webdriver.Chrome('/Users/joseh/Downloads/chromedriverfolder/chromedriver')

driver.get('https://lp.espacenet.com/?locale=es_LP')

search_box = driver.find_element(By.NAME,'query')

#Termino para la búsqueda
search_box.send_keys('agricultura')

#Sometemos búsqueda
search_box.send_keys(Keys.RETURN)

# Añadimos una pausa para que el sistema tenga tiempo a completar la busqueda
driver.implicitly_wait(10)

xpath1='//*[@id="publicationId1"]'
publicacion1= driver.find_element(By.XPATH,xpath1)
driver.execute_script("arguments[0].click();", publicacion1)
driver.implicitly_wait(10)

for i in range(1,16):

    xpathres='//*[@id="body"]/div[2]/p[1]'
    xpathtitulo='//*[@id="pagebody"]/h3'
    titulo= driver.find_element(By.XPATH,xpathtitulo).get_attribute('innerText')
    resumen=driver.find_element(By.XPATH,xpathres).get_attribute('innerText')
   
    data=[titulo,resumen]

    WebDriverWait(driver,10).until(EC.element_to_be_clickable((By.ID,'nextDocumentLink'))).click()

    with open('output.csv', 'a', encoding='UTF8', newline='') as f:
        writer= csv.writer(f)
        writer.writerow(data)

driver.close()

```
La última línea del programa cierra el driver para no dejar el buscador automático corriendo. 

Al final, este programa debe producir un documento csv con los primeros 8 títulos y resúmenes de las patentes. Se producen solamente 8 aunque se pasa por el loop 16 veces porque la página web tiene un error de doble clic en el cursor de siguiente y repite las entradas dos veces. Esta repetición se puede limpiar después con herramientas como Excel o OpenRefine o utilizando Python más complicado como un __if__ para comparar las entradas y borrar las repetidas.

El CSV abre como un documento normal en microsoft Excel, Libreoffice o en editores de texto.

## Conclusión

Con esta lección hemos cubierto los elementos básicos de Selenium en Python desde como instalarlo hasta como extraer datos con su ayuda. Para continuar u desarrollo en Selenium por favor consulte su documentación <a href="https://www.selenium.dev/documentation/">aquí</a> y <a href="https://selenium-python.readthedocs.io/installation.html">aquí</a> ¡Espero que este conocimiento le sirva de base para sus proyectos investigativos!
