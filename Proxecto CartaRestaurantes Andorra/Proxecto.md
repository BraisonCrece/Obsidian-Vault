Organización da Base de Datos:
![[esquema_CartasHoteles.jpeg]]

## <span style='color:#eb3b5a'> Policies, control de acceso</span>:
https://github.com/CanCanCommunity/cancancan
podese usar CanCanCan ou facelo a man. (Mirar curso aprendev)

Para implementar políticas en tu aplicación Ruby on Rails, puedes seguir los siguientes pasos:

1.  Crear una política: Puedes crear una nueva política utilizando el generador de políticas de Rails. Ejemplo: `rails generate policy Post`
2.  Configurar la política: Dentro de la política, puedes definir los permisos y restricciones para cada acción que se pueda realizar en el recurso. Ejemplo:

```rb
class PostPolicy
  def initialize(user, post)
    @user = user
    @post = post
  end

  def show?
    true
  end

  def edit?
    @user == @post.author
  end

  def destroy?
    @user == @post.author || @user.admin?
  end
end
```

En este ejemplo, la política de `Post` define que cualquier usuario puede ver un post (`show?`), pero solo el autor del post puede editarlo (`edit?`). Además, un usuario con permisos de administrador también puede eliminar un post (`destroy?`).

3.  Utilizar la política: Para utilizar la política en la aplicación, puedes llamar a los métodos de la política en el controlador de Rails. Ejemplo:
```rb
class PostsController < ApplicationController
  def show
    @post = Post.find(params[:id])
    authorize @post
  end

  def edit
    @post = Post.find(params[:id])
    authorize @post
  end

  def update
    @post = Post.find(params[:id])
    authorize @post
    if @post.update(post_params)
      redirect_to @post
    else
      render 'edit'
    end
  end

  def destroy
    @post = Post.find(params[:id])
    authorize @post
    @post.destroy
    redirect_to posts_path
  end
end
```

En este ejemplo, se utiliza el método `authorize` para verificar si el usuario actual tiene permiso para realizar la acción correspondiente en el post. Si el usuario no tiene permiso, se redireccionará a otra página o se mostrará un mensaje de error.


## <span style='color:#eb3b5a'>Log personalizado</span>:

Creamos un modelo ChangeLog, que poida acceder aos datos do usuario e dos productos (ou platos ou chamalle X)
```rb
# app/models/change_log.rb
class ChangeLog < ApplicationRecord
  belongs_to :user
  belongs_to :product
end
```

Luego, puedes agregar los callbacks en el modelo de `Product` para que se ejecute una acción después de que se guarden, actualicen o eliminen registros en la base de datos. Para esto, puedes utilizar los métodos `after_create`, `after_update` y `after_destroy`. Por ejemplo:

```rb
# app/models/product.rb

class Product < ApplicationRecord
  after_create :log_create
  after_update :log_update
  after_destroy :log_destroy
  
  private
  
  def log_create
    ChangeLog.create(
      action: "create",
      product: self,
      user: current_user, # Aquí debes obtener el usuario actual
      email: current_user.email, # Aquí debes obtener el email del usuario actual
      created_at: created_at
    )
  end
  
  def log_update
    ChangeLog.create(
      action: "update",
      product: self,
      user: current_user, # Aquí debes obtener el usuario actual
      email: current_user.email, # Aquí debes obtener el email del usuario actual
      created_at: created_at
    )
  end
  
  def log_destroy
    ChangeLog.create(
      action: "destroy",
      product: self,
      user: current_user, # Aquí debes obtener el usuario actual
      email: current_user.email, # Aquí debes obtener el email del usuario actual
      created_at: created_at
    )
  end
end
```

En estos callbacks, se crea un registro en la tabla de `ChangeLog` con la información que deseas guardar. El campo `action` indica la acción que se realizó (crear, actualizar o eliminar). El campo `product` hace referencia al producto que se modificó. Los campos `user` y `email` hacen referencia al usuario que realizó la acción. Por último, el campo `created_at` guarda la fecha en la que se realizó la acción.


## <span style='color:#eb3b5a'>Posible estructura da BDD con respecto ás traduccións</span>:
La estructura de la base de datos normalizada podría ser la siguiente:

```vb
platos:
  id: integer
  precio: decimal
  categoria_id: integer
  ...

traducciones:
  id: integer
  plato_id: integer
  idioma: string
  nombre: string
  descripcion: text
```

En este ejemplo, la tabla "platos" tiene una columna "categoria_id" que se relaciona con la tabla "categorias" para evitar la repetición de datos. La tabla "traducciones" tiene una columna "plato_id" que se relaciona con la tabla "platos" y contiene los datos de las traducciones para cada idioma.
