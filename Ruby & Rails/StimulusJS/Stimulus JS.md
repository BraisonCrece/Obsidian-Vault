# Como podemos asignar a un botón la acción <span style='color:#eb3b5a'>Copiar al portapapeles</span>?
Primero, tendremos que tener el controlador asociado al elemento. 
Podemos hacerlo del siguiente modo:
```html
<div 
data-controller="share-modal" 
data-share-url="<%= property_url(@property) %>"
> 
 	<div data-action="click->share-modal#copyLink">
		Copy to clickboard!
	</div>
</div>
```

Si nos fijamos, podemos ver que tenemos el Stimulus controller asociado al div donde tenemos el "botón"(<span style='color:#f7b731'>data-controller</span>=<span style='color:#20bf6b'>"share-modal"</span>).
También tenemos un <span style='color:#f7b731'>data-share-url</span> donde guardaremos la información que queramos utilizar, en este caso la Url de la propiedad.
Luego, definimos un "event listener", mediante el atributo 
<span style='color:#f7b731'>data-action</span>=<span style='color:#20bf6b'>"click->share-modal#copyLink"</span>, que tiene la siguiente estructura:
* acción -> click
* nombre del controlador -> share-modal (share_modal_controller.js)
* nombre del método: copyLink (copyLink())

Luego definimos la acción dentro del controlador:
```js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
	copyLink() {
		navigator.clipboard.writeText(
			this.element.dataset.shareUrl).then(() => {
				alert('Link copied to clipboard');
			})
		}
	}
}
```
