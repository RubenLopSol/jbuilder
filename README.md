# Conceptos Básicos

1. ¿Qué es DevOps y por qué es importante en el desarrollo de software?

    DevOps es una metodología en el desarrollo de software que utiliza herramientas para resolver problemas entre los equipos de desarrollo (Dev) y operaciones (Ops). Promueve la colaboración entre estos equipos y automatiza los procesos para hacer el desarrollo y la implementación de software más eficientes, rápidos y confiables. Antes de DevOps, los equipos de desarrollo y operaciones trabajaban por separado, lo que generaba barreras en el flujo de trabajo. DevOps permite una cultura en la que el equipo de desarrollo entiende cómo su software se ejecuta en el hardware y el equipo de operaciones comprende cómo se crea el software. Esto mejora significativamente la colaboración y los resultados.

2. ¿Cuáles son los beneficios clave de usar servicios en la nube como AWS?

    Creo que uno de los principales beneficios de utilizar servicios en la nube es la escalabilidad, lo que significa que pagas solo por el uso que haces. Esto se traduce en eficiencia de costos y flexibilidad, ya que reduce el costo de poseer los recursos y no utilizarlos todo el tiempo.

3. ¿Qué conceptos se deberían de tener en cuenta al desarrollar aplicaciones en la nube?

    - Diseño escalable.
    - Alta disponibilidad.
    - Segurida (SSL, MFA...)
    - Gestionar los gastos en recursos


4. ¿Qué es Kubernetes y cómo se diferencia de Docker?

    **Kubernetes** es una plataforma con la cual podemos orquestar nuestros contenedores mediante la generación de clústeres con **Master nodes** y **Worker nodes**. El master se encarga de orquestar el trabajo a realizar por los workers a través de su API server; esta es la conexión con el exterior y la conexión con los workers. En los workers es donde se almacenan los contenedores, dentro de los pods, y cada worker necesita tener su propio container runtime (Docker), que nos permitirá crear, eliminar y ejecutar contenedores.

    - **Docker:** permite crear, ejecutar y eliminar contenedores (entorno virtual).
    - **Kubernetes:** nos permite controlar y crear las conexiones entre estos contenedores para que sean capaces de trabajar juntos.

    

5. Explica el papel de Helm en Kubernetes.

    El manejo de Kubernetes es mediante archivos .yaml específicos (pod, deployment, service...). Helm nos facilita el manejo de Kubernetes, empaquetando todos los archivos .yaml que necesitamos para nuestra aplicación, y Kubernetes recibe y es capaz de interpretar y ejecutar este paquete.

    No tengo experiencia práctica, pero esto es lo que entiendo sobre Helm.


6. ¿Cuáles son los componentes clave de un pipeline de CI/CD?

    Una **Pipeline** de CI/CD (Integración Continua, Entrega Continua) comienza con el código fuente almacenado en un sistema de control de versiones como GitHub, GitLab, Bitbucket, etc. Cuando tenemos el código en un sistema de control de versiones, permite la colaboración entre varios equipos y personas.

    **Integración Continua (CI):** Esta etapa incluye integrar el código e iniciar la pipeline de integración para construir (el código fuente se compila y se transforma en una aplicación ejecutable o artefacto, como una imagen de Docker o un archivo JAR) y la etapa de pruebas (pruebas unitarias, de integración y de extremo a extremo). Luego, se almacena el artefacto generado en un registro de Docker.

    **Entrega Continua (CD):** Esta etapa automatiza el proceso de despliegue utilizando diferentes estrategias como Canary o Blue-Green en entornos como desarrollo, beta y producción. Este paso significa que el artefacto creado en CI queremos desplegarlo en recursos de AWS, Azure.. que construimos usando Terraform. También incluye monitoreo y rollback, entre otros.



----
# Jbuilder

Jbuilder gives you a simple DSL for declaring JSON structures that beats
manipulating giant hash structures. This is particularly helpful when the
generation process is fraught with conditionals and loops. Here's a simple
example:

``` ruby
# app/views/messages/show.json.jbuilder

json.content format_content(@message.content)
json.(@message, :created_at, :updated_at)

json.author do
  json.name @message.creator.name.familiar
  json.email_address @message.creator.email_address_with_name
  json.url url_for(@message.creator, format: :json)
end

if current_user.admin?
  json.visitors calculate_visitors(@message)
end

json.comments @message.comments, :content, :created_at

json.attachments @message.attachments do |attachment|
  json.filename attachment.filename
  json.url url_for(attachment)
end
```

This will build the following structure:

``` javascript
{
  "content": "<p>This is <i>serious</i> monkey business</p>",
  "created_at": "2011-10-29T20:45:28-05:00",
  "updated_at": "2011-10-29T20:45:28-05:00",

  "author": {
    "name": "David H.",
    "email_address": "'David Heinemeier Hansson' <david@heinemeierhansson.com>",
    "url": "http://example.com/users/1-david.json"
  },

  "visitors": 15,

  "comments": [
    { "content": "Hello everyone!", "created_at": "2011-10-29T20:45:28-05:00" },
    { "content": "To you my good sir!", "created_at": "2011-10-29T20:47:28-05:00" }
  ],

  "attachments": [
    { "filename": "forecast.xls", "url": "http://example.com/downloads/forecast.xls" },
    { "filename": "presentation.pdf", "url": "http://example.com/downloads/presentation.pdf" }
  ]
}
```

To define attribute and structure names dynamically, use the `set!` method:

``` ruby
json.set! :author do
  json.set! :name, 'David'
end

# => {"author": { "name": "David" }}
```


To merge existing hash or array to current context:

``` ruby
hash = { author: { name: "David" } }
json.post do
  json.title "Merge HOWTO"
  json.merge! hash
end

# => "post": { "title": "Merge HOWTO", "author": { "name": "David" } }
```

Top level arrays can be handled directly.  Useful for index and other collection actions.

``` ruby
# @comments = @post.comments

json.array! @comments do |comment|
  next if comment.marked_as_spam_by?(current_user)

  json.body comment.body
  json.author do
    json.first_name comment.author.first_name
    json.last_name comment.author.last_name
  end
end

# => [ { "body": "great post...", "author": { "first_name": "Joe", "last_name": "Bloe" }} ]
```

You can also extract attributes from array directly.

``` ruby
# @people = People.all

json.array! @people, :id, :name

# => [ { "id": 1, "name": "David" }, { "id": 2, "name": "Jamie" } ]
```

To make a plain array without keys, construct and pass in a standard Ruby array.

```ruby
my_array = %w(David Jamie)

json.people my_array

# => "people": [ "David", "Jamie" ]
```

You don't always have or need a collection when building an array.

```ruby
json.people do
  json.child! do
    json.id 1
    json.name 'David'
  end
  json.child! do
    json.id 2
    json.name 'Jamie'
  end
end

# => { "people": [ { "id": 1, "name": "David" }, { "id": 2, "name": "Jamie" } ] }
```

Jbuilder objects can be directly nested inside each other.  Useful for composing objects.

``` ruby
class Person
  # ... Class Definition ... #
  def to_builder
    Jbuilder.new do |person|
      person.(self, :name, :age)
    end
  end
end

class Company
  # ... Class Definition ... #
  def to_builder
    Jbuilder.new do |company|
      company.name name
      company.president president.to_builder
    end
  end
end

company = Company.new('Doodle Corp', Person.new('John Stobs', 58))
company.to_builder.target!

# => {"name":"Doodle Corp","president":{"name":"John Stobs","age":58}}
```

You can either use Jbuilder stand-alone or directly as an ActionView template
language. When required in Rails, you can create views à la show.json.jbuilder
(the json is already yielded):

``` ruby
# Any helpers available to views are available to the builder
json.content format_content(@message.content)
json.(@message, :created_at, :updated_at)

json.author do
  json.name @message.creator.name.familiar
  json.email_address @message.creator.email_address_with_name
  json.url url_for(@message.creator, format: :json)
end

if current_user.admin?
  json.visitors calculate_visitors(@message)
end
```

You can use partials as well. The following will render the file
`views/comments/_comments.json.jbuilder`, and set a local variable
`comments` with all this message's comments, which you can use inside
the partial.

```ruby
json.partial! 'comments/comments', comments: @message.comments
```

It's also possible to render collections of partials:

```ruby
json.array! @posts, partial: 'posts/post', as: :post

# or
json.partial! 'posts/post', collection: @posts, as: :post

# or
json.partial! partial: 'posts/post', collection: @posts, as: :post

# or
json.comments @post.comments, partial: 'comments/comment', as: :comment
```

The `as: :some_symbol` is used with partials. It will take care of mapping the passed in object to a variable for the
partial. If the value is a collection either implicitly or explicitly by using the `collection:` option, then each
value of the collection is passed to the partial as the variable `some_symbol`. If the value is a singular object,
then the object is passed to the partial as the variable `some_symbol`.

Be sure not to confuse the `as:` option to mean nesting of the partial. For example:

```ruby
 # Use the default `views/comments/_comment.json.jbuilder`, putting @comment as the comment local variable.
 # Note, `comment` attributes are "inlined".
 json.partial! @comment, as: :comment
```

is quite different from:

```ruby
 # comment attributes are nested under a "comment" property
json.comment do
  json.partial! "/comments/comment.json.jbuilder", comment: @comment
end
```

You can pass any objects into partial templates with or without `:locals` option.

```ruby
json.partial! 'sub_template', locals: { user: user }

# or

json.partial! 'sub_template', user: user
```


You can explicitly make Jbuilder object return null if you want:

``` ruby
json.extract! @post, :id, :title, :content, :published_at
json.author do
  if @post.anonymous?
    json.null! # or json.nil!
  else
    json.first_name @post.author_first_name
    json.last_name @post.author_last_name
  end
end
```

To prevent Jbuilder from including null values in the output, you can use the `ignore_nil!` method:

```ruby
json.ignore_nil!
json.foo nil
json.bar "bar"
# => { "bar": "bar" }
```

## Caching

Fragment caching is supported, it uses `Rails.cache` and works like caching in
HTML templates:

```ruby
json.cache! ['v1', @person], expires_in: 10.minutes do
  json.extract! @person, :name, :age
end
```

You can also conditionally cache a block by using `cache_if!` like this:

```ruby
json.cache_if! !admin?, ['v1', @person], expires_in: 10.minutes do
  json.extract! @person, :name, :age
end
```

Aside from that, the `:cached` options on collection rendering is available on Rails >= 6.0. This will cache the
rendered results effectively using the multi fetch feature.

```ruby
json.array! @posts, partial: "posts/post", as: :post, cached: true

# or:
json.comments @post.comments, partial: "comments/comment", as: :comment, cached: true
```

If your collection cache depends on multiple sources (try to avoid this to keep things simple), you can name all these dependencies as part of a block that returns an array:

```ruby
json.array! @posts, partial: "posts/post", as: :post, cached: -> post { [post, current_user] }
```

This will include both records as part of the cache key and updating either of them will expire the cache.

## Formatting Keys

Keys can be auto formatted using `key_format!`, this can be used to convert
keynames from the standard ruby_format to camelCase:

``` ruby
json.key_format! camelize: :lower
json.first_name 'David'

# => { "firstName": "David" }
```

You can set this globally with the class method `key_format` (from inside your
environment.rb for example):

``` ruby
Jbuilder.key_format camelize: :lower
```

By default, key format is not applied to keys of hashes that are
passed to methods like `set!`, `array!` or `merge!`. You can opt into
deeply transforming these as well:

``` ruby
json.key_format! camelize: :lower
json.deep_format_keys!
json.settings([{some_value: "abc"}])

# => { "settings": [{ "someValue": "abc" }]}
```

You can set this globally with the class method `deep_format_keys` (from inside your
environment.rb for example):

``` ruby
Jbuilder.deep_format_keys true
```

## Testing JBuilder Response body with RSpec

To test the response body of your controller spec, enable `render_views` in your RSpec context. This [configuration](https://rspec.info/features/6-0/rspec-rails/controller-specs/render-views) renders the views in a controller test.

## Contributing to Jbuilder

Jbuilder is the work of many contributors. You're encouraged to submit pull requests, propose
features and discuss issues.

See [CONTRIBUTING](CONTRIBUTING.md).

## License
Jbuilder is released under the [MIT License](http://www.opensource.org/licenses/MIT).
