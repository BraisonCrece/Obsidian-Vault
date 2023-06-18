---
sticker: 1f3a1
banner: https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgga753X_rzMozmHACbUidDu3v4NiRcLhdU5LHNDeNLwpZEdQDjnjEs1XnmxPPklrW3LvTHFWgnTaGPP_GdmJEo3JrLZh1KgDpDF41-DvfTxKEMJ8vWrP2dvfY-tIeulRwTol3HnoqWzwMxQYhY3PdOmSmcXGZpgNe_ciCUNsnBEySsqVjjZ8KsaihB/s1600/ALS_Zeitwerk_2022_Platinum_TAW-010.jpg
---
Zeitwerk es un código cargador eficiente y conveniente para proyectos Ruby y Ruby on Rails. Fue introducido en Rails 6 como el cargador de código por defecto, reemplazando el antiguo modo "clásico".

El término "switch to zeitwerk mode" se refiere a cambiar el modo de carga de código en una aplicación Rails a Zeitwerk.

Aquí hay algunos puntos clave sobre Zeitwerk:

1. **Carga perezosa (Lazy Loading)**: Zeitwerk carga las clases y módulos de Ruby a demanda, lo que significa que solo se cargan cuando se necesitan. Esto puede hacer que tu aplicación sea más eficiente, ya que no necesita cargar todo el código al inicio.
    
2. **Convención sobre configuración**: Zeitwerk sigue la filosofía de "convención sobre configuración" de Rails. Esto significa que si sigues las convenciones de nomenclatura de archivos y directorios de Rails, Zeitwerk puede cargar automáticamente tu código sin necesidad de `require` o `require_dependency`.
    
3. **Recarga de código en desarrollo**: En el entorno de desarrollo, Zeitwerk puede recargar el código automáticamente cuando se hacen cambios. Esto significa que no necesitas reiniciar el servidor cada vez que haces un cambio en el código.
    
4. **Compatibilidad con modos de carga**: Rails 6 y posteriores permiten cambiar entre Zeitwerk y el modo de carga clásico. Esto es útil si tienes una aplicación existente que depende del comportamiento de carga clásico.
    

Para cambiar a Zeitwerk en una aplicación Rails existente, necesitas configurar la opción `config.autoloader` en tu archivo `config/application.rb`:

rubyCopy code

`module YourApp   class Application < Rails::Application     config.load_defaults 6.0     config.autoloader = :zeitwerk   end end`

Después de hacer esto, necesitas asegurarte de que tu código sigue las convenciones de Zeitwerk. Esto puede implicar renombrar archivos o directorios, o cambiar la forma en que se definen las clases y módulos.

Por último, es importante mencionar que Zeitwerk es una característica de Rails 6 y posteriores. Si estás utilizando una versión anterior de Rails, es posible que necesites actualizar Rails para poder utilizar Zeitwerk.