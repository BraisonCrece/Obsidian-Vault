## <span style='color:#2d98da'>Podemos referenciar a la  última salida de la consola con el caracter <span style='color:#eb3b5a'>_</span></span>
Por ejemplo, ejecutamos el siguiente comando de consulta:
```ruby
Post.last
# output --> #<Post:0x00007f16062b56b0  id: 41, title: "titulo actualizado", content: "content", published: true, user_id: 1,  created_at: Mon, 19 Dec 2022 11:54:57.151198000 UTC +00:00, updated_at: Mon, 19 Dec 2022 12:12:56.367925000 UTC +00:00> 
```
Pues bien, podemos referenciar esta salida con el operador __ del siguiente modo:
```ruby
_.id
# output --> => 41
```


