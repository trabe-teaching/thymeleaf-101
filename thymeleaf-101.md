title: Thymeleaf 101
theme: trabe-teaching/cleaver-theme
output: index.html
controls: false
progress: false


-- cover

# Thymeleaf 101
## http://ir.gl/thymeleaf

--

# asís y marcos 

asis.garcia@trabesoluciones.com
marcos.abel@trabesoluciones.com

-- image

![Trabe](images/trabe.png)

--

NOTA: parte de los materiales de esta charla están tomados y/o adaptados (con permiso del autor) de la documentación oficial del proyecto disponible en http://www.thymeleaf.org/

-- section 

Maneras de generar HTML

-- bullets toc bullets-first

## Introducción a thymeleaf

* Qué es thymeleaf y qué tiene que ver con natural templating
* Quién usa thymeleaf
* Qué pinta tiene thymeleaf. Componentes básicos
* Atributos básicos y Layouts
* Configurar Spring MVC para utilizar Thymeleaf como capa vista

-- section

## Qué es thymeleaf

--

* Librería java. Open source ;). Made in Galicia
* Motor de plantillas XML / HTML / HTML5
* Utilizable de forma "independiente"
* _Logicless_

--

* Intergración "out of the box" con Spring MVC
* Extensible. Framework de motores de plantillas
* Soporte para _natural templating_

--

## ¿natural what?

--

## Natural Templating

Las plantillas de la aplicación deberían ser prototipos válidos para trabajar sobre ellos e idealmente visualizables en un navegador sin necesidad de ser procesadas.

--
## Permite agilizar el proceso de desarrollo

* Facilita comunicación con el cliente 
* Diseñadores y desarrolladores pueden trabajar en paralelo
* Los diseñadores no necesitan herramientas ni conocimientos específicos
-- code sligthly-smaller
## "Natural templating" en jsp (I/II)

```xml
<%@ taglib prefix="sf" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="s" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <title>Spring MVC view layer: Thymeleaf vs. JSP</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="<s:url value='/css/thvsjsp.css' />"/>
  </head>

  <body>
    <h2>This is a JSP</h2>
    <s:url var="formUrl" value="/subscribejsp" />
    <sf:form modelAttribute="subscription" action="${formUrl}">
      <div>
        <label for="email"><s:message code="subscription.email" />:
        </label>
        <sf:input path="email" />
      </div>
```

-- code sligthly-smaller

## "Natural templating" en jsp (II/II)

```xml
      <div>
        <label><s:message code="subscription.type" />: </label>
        <ul>
          <c:forEach var="type" items="${allTypes}" varStatus="typeStatus">
            <li>
              <sf:radiobutton path="subscriptionType" value="${type}" />
              <label for="subscriptionType${typeStatus.count}">
                <s:message code="subscriptionType.${type}" />
              </label>
            </li>
          </c:forEach>
        </ul>
      </div>

      <div class="submit">
        <button type="submit" name="save">
          <s:message code="subscription.submit" />
        </button>
      </div>
    </sf:form>
  </body>
</html>
```

-- image


![Jsp renderizado estáticamente](images/jsp-statically-rendered.png)

-- image

![WTF](images/wtf-1.jpg)

-- image


![Jsp renderizado estáticamente](images/jsp-statically-rendered.png)

-- code sligthly-smaller

## Natural templating con thymeleaf (I/II)

```xml
<DOCCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>
    <title>Spring MVC view layer: Thymeleaf vs. JSP</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all"
          href="../../css/thvsjsp.css" th:href="@{/css/thvsjsp.css}"/>
  </head>

  <body>

    <h2>This is a Thymeleaf template</h2>

    <form action="#" th:object="${subscription}" 
      th:action="@{/subscribeth}">
      <div>
        <label for="email" th:text="#{subscription.email}">Email:</label>
        <input type="text" th:field="*{email}" />
      </div>
```

-- code sligthly-smaller

## Natural templating con thymeleaf (II/II)

```xml
      <div>
        <label th:text="#{subscription.type}">Type: </label>
        <ul>
          <li th:each="type : ${allTypes}">
            <input type="radio" th:field="*{subscriptionType}" 
                   th:value="${type}" />
            <label th:for="${#ids.prev('subscriptionType')}"
              th:text="#{'subscriptionType.'+${type}}">First type</label>
          </li>
          <li th:remove="all"><input type="radio" />
              <label>Second Type</label>
          </li>
        </ul>
      </div>
      <div class="submit">
        <button type="submit" name="save" th:text="#{subscription.submit}">
          Subscribe me!
        </button>
      </div>
    </form>
  </body>
</html>

```

-- image

![Plantilla Thymeleaf renderizada estáticamente](images/th-statically-rendered.png)

-- image

![WTF](images/cool-1.png)

-- section

## Quién usa thymeleaf

--

http://www.thymeleaf.org/whoisusingthymeleaf.html

--

Tecnología en crecimiento, promocionada directamente por spring.io

http://spring.io/guides/tutorials/web/4/

-- section

## Qué pinta tiene Thymeleaf

-- code

```xml



<table>
  <thead>
    <tr>
      <th th:text="#{msgs.headers.name}">Name</th>
      <th th:text="#{msgs.headers.price}">Price</th>
    </tr>
  </thead>
  <tbody>
    <tr th:each="prod : ${allProducts}">
      <td th:text="${prod.name}">Oranges</td>
      <td th:text="${#numbers.formatDecimal(prod.price,1,2)}">0.99</td>
    </tr>
  </tbody>
</table>
```

-- code

```xml



<table>
    <tr data-th-each="user : ${users}">
        <td data-th-text="${user.login}">...</td>
        <td data-th-text="${user.name}">...</td>
    </tr>
</table>
```

-- code sligthly-smaller

## Contexto de una plantilla

```java
public interface IContext {

    public VariablesMap<String,Object> getVariables();
    public Locale getLocale();
    ...
}

public interface IWebContext extends IContext {

    public HttpSerlvetRequest getHttpServletRequest();
    public HttpSession getHttpSession();
    public ServletContext getServletContext();

    public VariablesMap<String,String[]> getRequestParameters();
    public VariablesMap<String,Object> getRequestAttributes();
    public VariablesMap<String,Object> getSessionAttributes();
    public VariablesMap<String,Object> getApplicationAttributes();

}
```

-- code

## Implementación de serie de contexto web

```java



WebContext ctx = new WebContext(request, response,
    servletContext, request.getLocale());
```

Añade los atributos de la _request_ al _mapa de variables del contexto_

-- bullets


Añade las siguientes variables de contexto:
* _param_: contiene los parámetros de la _request_
* _session_: contiene los atributos de la sesión
* _application_: contiene los atributos del _ServletContext_

-- section

## Dialecto Standard de thymeleaf

--

## Expresiones

* OGNL (Object-Graph Navigation Language) o SpringEL
* Tienen acceso al contexto global de la plantilla

```xml
<div th:text="${session.user.name}"/>
<li th:each="book : ${books}">
```

--

## Expresiones asterisco

Se ejecutan sobre un contexto previamente definido

```xml
<span th:text="*{book.author}"/>

<div th:object="${book}">
	<span th:text="*{title}"></span>
</div>
```

--

## Expresiones de externalización de cadenas

Se sustituyen por mensajes localizados, típicamente definidos en ficheros .properties

```xml
<span th:text="#{app.messages.title}"></span>
```

--

## Expresiones de urls

Reescritura de urls, con soporte para parámetros y URLs relativas o absolutas

```xml
<form th:action="@{/createOrder(id=${orderId})}">
<a href="main.html" th:href="@{../main.html}">
</form>
```
--

## Literales

* Texto: 'one text', 'Another one!',…
* Números: 0, 34, 3.0, 12.3,…
* Boolean: true, false
* ...…
--

## Comparación e igualdad

* &gt;, <, >=, <= (gt, lt, ge, le)
* ==, != (eq, ne)
--

## Operadores condicionales

* If-then: (if) ? (then)
* If-then-else: (if) ? (then) : (else)
* Default: (value) ?: (defaultvalue)

--

## Operaciones

--

## Texto

* Concatenación de cadenas: +
* Interpolación: |The name is ${name}|

--

## Aritméticas

* Operaciones aritméticas:
* Operadores binarios: +, -, *, /, %
* Signo negativo (operador unario): -

--

## Booleanas

* Operadores binarios: and, or
* Negación (operador unario): !, not

-- section

## Atributos básicos

-- code

## th:text y th:utext

sustituyen el contenido de un tag por el resultado de evaluar la expresión que contenga el atributo.

```xml
home.welcome=Welcome to our <b>fantastic</b> grocery store!

<!-- escapando -->
<p th:text="#{home.welcome}">Welcome to our grocery store!</p>

<p>Welcome to our &lt;b&gt;fantastic&lt;/b&gt; grocery store!</p>


<!-- sin escapar -->
<p th:utext="#{home.welcome}">Welcome to our grocery store!</p>

<p>Welcome to our <b>fantastic</b> grocery store!</p>
```

-- code sligthly-smaller

## th:attr

sustituye el valor de uno o más atributos del tag.

```xml
<input type="submit" value="Subscribe me!"
       th:attr="value=#{subscribe.submit}"/>
<img src="../../images/gtvglogo.png"
     th:attr="src=@{/images/gtvglogo.png},title=#{logo},alt=#{logo}" />
```

-- code sligthly-smaller

## th:value, th:action, th:href

```xml


<input type="submit" value="Subscribe me!"
       th:value="#{subscribe.submit}"/>

<form action="subscribe.html" th:action="@{/subscribe}">

<li>
  <a href="product/list.html" th:href="@{/product/list}">
    Product List
  </a>
</li>
```

-- code sligthly-smaller

## th:attrappend, th:attrprepend y th:classappend

```xml


<input type="button" class="btn"
       th:attrappend="class=${' ' + cssStyle}" />

<input type="button" class="btn"
       th:attrprepend="class=${cssStyle + ' '}" />

<tr th:each="prod : ${prods}" class="row"
    th:classappend="${prodStat.odd}? 'odd'">
```

--

th:abbr	th:accept th:accept-charset
th:accesskey	th:action	th:align
th:alt	th:archive	th:audio
th:autocomplete	th:axis	th:background
th:bgcolor	th:border	th:cellpadding
th:cellspacing	th:challenge	th:charset
th:cite	th:class	th:classid
th:codebase	th:codetype	th:cols
th:colspan	th:compact	th:content
th:contenteditable	th:contextmenu	th:data
th:datetime	th:dir	th:draggable
th:dropzone	th:enctype	th:for
th:form	th:formaction	th:formenctype
th:formmethod	th:formtarget	th:frame
th:frameborder	th:headers	th:height
th:high	th:href	th:hreflang
th:hspace	th:http-equiv	th:icon
th:id	th:keytype	th:kind
th:label

--

th:lang	th:list
th:longdesc	th:low	th:manifest
th:marginheight	th:marginwidth	th:max
th:maxlength	th:media	th:method
th:min	th:name	th:optimum
th:pattern	th:placeholder	th:poster
th:preload	th:radiogroup	th:rel
th:rev	th:rows	th:rowspan
th:rules	th:sandbox	th:scheme
th:scope	th:scrolling	th:size
th:sizes	th:span	th:spellcheck
th:src	th:srclang	th:standby
th:start	th:step	th:style
th:summary	th:tabindex	th:target
th:title	th:type	th:usemap
th:value	th:valuetype	th:vspace
th:width	th:wrap	th:xmlbase
th:xmllang	th:xmlspace

-- code

## Atributos booleanos

```xml


<input type="checkbox" name="active" th:checked="${user.active}" />

<input type="checkbox" name="active" checked="checked" />
```

Otros atributos disponibles:

```
th:async th:autofocus th:autoplay th:checked th:controls
th:declare th:default th:defer th:disabled th:formnovalidate
th:hidden th:ismap th:loop th:multiple th:novalidate
th:nowrap th:open th:pubdate th:readonly th:required
th:reversed th:scoped	th:seamless th:selected

```

-- code sligthly-smaller

## Iteración (I/II): ejemplo

```java
ProductService productService = new ProductService();
List<Product> allProducts = productService.findAll();

WebContext ctx = new WebContext(request, servletContext,
                                request.getLocale());
ctx.setVariable("prods", allProducts);

templateEngine.process("product/list", ctx, response.getWriter());

```

```xml
<table>
      <tr>
        <th>NAME</th>
        <th>PRICE</th>
        <th>IN STOCK</th>
      </tr>
      <tr th:each="prod : ${prods}">
        <td th:text="${prod.name}">Onions</td>
        <td th:text="${prod.price}">2.41</td>
        <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
      </tr>
</table>
```

-- code

## Iteración (II/II): objetos iterables

* java.util.List
* java.util.Iterable
* java.util.Map
* Arrays

-- code

## Evaluación condicional (I/II)

```xml


<a href="comments.html"
   th:href="@{/product/comments(prodId=${prod.id})}"
   th:if="${not #lists.isEmpty(prod.comments)}">view</a>

<a href="comments.html"
   th:href="@{/comments(prodId=${prod.id})}"
   th:unless="${#lists.isEmpty(prod.comments)}">view</a>
```

-- code

## Evalución condicional (II/II)

```xml


<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```

-- section

## Layouts

-- code sligthly-smaller

## layout.html
```xml


<!DOCTYPE html SYSTEM
  "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">

<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">

  <body>

    <footer th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>

  </body>

</html>
```

-- code sligthly-smaller
## index.html

```xml


<body>
  <div th:include="layout :: copy"></div>
</body>
```

-- code sligthly-smaller

## layout.html

```xml


<!DOCTYPE html SYSTEM
  "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">

<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">

  <body>

    <footer id="copy-id">
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>

  </body>

</html>
```

-- code sligthly-smaller

## index.html

```xml


<body>
  <div th:include="layout :: [#copy-id]"></div>
</body>
```


-- code sligthly-smaller

## th:include vs th:replace (I/II)


```xml

<!DOCTYPE html SYSTEM
  "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">

<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">

  <body>
    <footer th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
  </body>
</html>

<body>
  ...

  <div th:include="layout :: copy"></div>
  <div th:replace="layout :: copy"></div>
</body>
```

-- code sligthly-smaller

## th:include vs th:replace (II/II)


```xml


<body>

  ...

  <div>
    &copy; 2011 The Good Thymes Virtual Grocery
  </div>
  <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
  </footer>

</body>
```

-- code sligthly-smaller

## Fragmentos con parámetros

```xml


<div th:fragment="frag (onevar,twovar)">
    <p th:text="${onevar} + ' - ' + ${twovar}">...</p>
</div>
```

incluir un fragmento con parámetros:

```xml
<div th:include="::frag (${value1},${value2})">...</div>
<div th:include="::frag (onevar=${value1},twovar=${value2})">...</div>
```

-- code sligthly-smaller

## Eliminar fragmentos (I/II)

```xml
<tbody th:remove="all-but-first">
    <tr th:each="prod : ${prods}" th:class="${prodStat.odd}? 'odd'">
      <td th:text="${prod.name}">Onions</td>
      <td th:text="${prod.price}">2.41</td>
      <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
      <td>
        <span th:text="${#lists.size(prod.comments)}">2</span> comment/s
        <a href="comments.html"
           th:href="@{/product/comments(prodId=${prod.id})}"
           th:unless="${#lists.isEmpty(prod.comments)}">view</a>
      </td>
    </tr>
    <tr class="odd">
      <td>Blue Lettuce</td>
      <td>9.55</td>
      <td>no</td>
      <td>
        <span>0</span> comment/s
      </td>
    </tr>
  </tbody>
```

--

## Eliminar fragmentos (II/II)

Distintos modos de funcionamiento
* _all_: elimina tag y todo el contenido.
* _body_: conserva el tag, eliminando el contenido.
* _tag_: elimina el tag, preservando el contenido.
* _all-but-first_: elimina los hijos del  tag, excepto el primero.
* _none_: no elimina nada

-- section

# Integración con Spring MVC

-- bullets

* Podremos utilizar plantillas Thymeleaf para la capa vista
* Podremos utilizar Spring EL en las expresiones de las plantillas
* Gestión de formularios
* Integración a nivel de internacionalización (mensajes definidos en MessageSources)

--

![spring-mvc](images/spring_schema.jpg)

-- code sligthly-smaller
## Configuración (XML)

```xml
<bean id="templateResolver"
    class="org.thymeleaf.templateresolver.ServletContextTemplateResolver">
  <property name="prefix" value="/WEB-INF/templates/" />
  <property name="suffix" value=".html" />
  <property name="templateMode" value="HTML5" />
</bean>

<bean id="templateEngine"
      class="org.thymeleaf.spring4.SpringTemplateEngine">
  <property name="templateResolver" ref="templateResolver" />
</bean>

<bean class="org.thymeleaf.spring4.view.ThymeleafViewResolver">
  <property name="templateEngine" ref="templateEngine" />
  <property name="order" value="1" />
  <property name="viewNames" value="*.html,*.xhtml" />
</bean>
```

-- code sligthly-smaller

## Controlador

```java


...

@RequestMapping("/")
public String home() {
    ...
    return "home";
}

...
```

-- code sligthly-smaller
## Plantilla

```xml


<DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>
    <title>Home</title>
  </head>

  <body>
    <h2>Home!</h2>
  </body>
</html>
```

--

## más información

### http://www.thymeleaf.org/documentation.html
### @thymeleaf

--

# Thymeleaf.rb
## WIP. Wanna help?

* http://github.com/trabe/thymeleaf-rb
* http://github.com/trabe/thymeleaf-rails

--

# ¿dudas?

-- image

![Trabe](images/trabe.png)
