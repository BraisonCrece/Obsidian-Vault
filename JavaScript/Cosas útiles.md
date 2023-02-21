# Cómo generar un link de "<span style='color:#eb3b5a'>compartir en Twitter</span> / <span style='color:#eb3b5a'>WhatsApp</span> / <span style='color:#eb3b5a'>Facebook</span>"?
```js
// whatsapp
window.open("whatsapp://send?text=URL_A_COMPARTIR", "_blank")

// facebook
window.open("https://www.facebook.com/sharer/sharer.php?u=URL_A_COMPARTIR", "_blank")

//twitter
window.open("https://twitter.com/intent/tweet?url=URL_A_COMPARTIR", "_blank")
```

<span style='color:#0fb9b1'>window</span>.<span style='color:#f7b731'>open</span> sirve para ir a una nueva URL, en este caso personalizada para compartir el recurso vía una de estar redes sociales.