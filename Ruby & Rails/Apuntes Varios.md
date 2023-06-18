# Que es un <span style='color:#8854d0'>mock</span>
Un "mock" es un objeto o componente que simula el comportamiento de un objeto real en un entorno de prueba. Se utiliza para <span style='color:#eb3b5a'>aislar las pruebas de dependencias externas</span>, como servicios web o bases de datos, y para garantizar que las pruebas sean rápidas, predecibles y fáciles de mantener. Los mocks también permiten verificar que tu código interactúa correctamente con las dependencias externas, incluso si esas dependencias no están disponibles o no son confiables durante las pruebas.

<span style='color:#eb3b5a'>WebMock</span> es una gema popular en Ruby on Rails para crear mocks de solicitudes HTTP. Te permite interceptar y manipular solicitudes HTTP realizadas por tu aplicación durante las pruebas, y simular respuestas de servicios externos.

Un ejemplo:
Utilizamos WebMock para simular una respuesta HTTP en un test. 
Supongamos que deseamos probar la clase `JsonIPService` y simular la respuesta de "[https://www.jsonip.com](https://www.jsonip.com/)". Aquí hay un ejemplo de cómo hacerlo:

```rb
# test/json_ip_service_test.rb
require 'test_helper'
require 'webmock/minitest'

class JsonIPServiceTest < ActiveSupport::TestCase
  test "returns an IP address" do
    # Simula la respuesta del servicio externo
    stub_request(:get, "https://www.jsonip.com").
      to_return(body: '{"ip":"2a0c:5a80:3601:3e00:e0fe:5f3:b765:b817"}', headers: { 'Content-Type' => 'application/json' })

    # Verifica que JsonIPService.ip devuelve la IP esperada
    assert_equal "2a0c:5a80:3601:3e00:e0fe:5f3:b765:b817", JsonIPService.ip
  end
end
```


## Gema muy guay: <span style='color:#eb3b5a'>VCR</span>
VCR es una gema en Ruby que ayuda a <span style='color:#eb3b5a'>grabar y reproducir</span> interacciones HTTP para facilitar la prueba de aplicaciones que se comunican con servicios web externos. Al utilizar VCR, puedes grabar las respuestas HTTP reales de los servicios web y luego utilizar esas respuestas grabadas en tus pruebas en lugar de realizar solicitudes HTTP en tiempo real cada vez que se ejecutan las pruebas.

VCR es útil para:

1.  Hacer que las pruebas sean **<span style='color:#8854d0'>más rápidas</span>**, ya que no es necesario realizar solicitudes HTTP en tiempo real.
2.  Hacer que las pruebas sean **<span style='color:#8854d0'>más confiables</span>**, ya que no dependen de la disponibilidad o el estado de los servicios web externos.
3. **<span style='color:#8854d0'>Facilitar la verificación</span>** de que tus pruebas interactúan correctamente con los servicios web externos.

Hay dos conceptos principales en VCR: "cassettes" y "middleware". Un "cassette" es un archivo que contiene las respuestas HTTP grabadas, mientras que el "middleware" es un componente que se integra con las bibliotecas HTTP de Ruby para grabar y reproducir las respuestas.

Ejemplo:
Configuración:
```rb
require 'vcr' 
VCR.configure do |config| 
	config.cassette_library_dir = 'test/vcr_cassettes' 
	config.hook_into :webmock # Puedes usar otras bibliotecas como :faraday o :typhoeus end
```

En el test:
```rb
require 'test_helper'

class MyApiTest < ActiveSupport::TestCase
  test "fetches data from external API" do
    VCR.use_cassette('my_api_response') do
      result = MyApi.fetch_data
      assert_equal 'expected_data', result
    end
  end
end
```

También se puede configurar para que se genere un archivo cassete de cada test:
```rb
setup do
	VCR.insert_cassette(name)
end

teardown do
	VCR.eject_cassette
end
```

Así no habrá que meter cada aserción dentro de un bloque VCR. 🙂

