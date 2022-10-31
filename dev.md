
# RAILS MVC MİMARİSİ (MODEL, VIEW, CONTROLLER )

MVC mimarisinin anafikri kullanılan arayüz ile arka plandaki işlemlerin birbirinden ayrı şekilde ele alınmasıdır.
Bu yüzden Model, View, Controller olmak üzere 3 ayrı başlık altında geliştirilmiştir. Bu yapı sayesinde yapılan hatanın kaynağı çok daha kolay şekilde bulunabilir.

 MODEL NASIL ÇALIŞIR? 

Arkaplandaki tüm işlerin yapıldığı alandır. Veri tabanı ve işlemleriyle ilgilenirken, kullanıcı ve görünüm kısmıyla ilgilenmez.

VIEW NASIL ÇALIŞIR?

View katmanı işin görsel yani arayüz kısmını ifade eder. Burada kullanıcı ile iletişim kurulur ve arka planda yapılan işlemler burada gösterilmez.
Model katmanındaki işlemlerin gözükmesini sağlar ve genellikle HMTL CSS gibi teknolojiler kullanılır.
Burada sunucu controller sayesinde kullanıcıdan istek alır, İsteği modele götürür ve modelden dönen sonucu view‘e gönderir.
Böylece kullanıcı isteği gerçekleştirilmiş olur.

CONTROLLER NASIL ÇALIŞIR?

Kullanıcıdan gelen isteklerin işlendiği, view ve model arasında bağlantıyı kuran bir köprü olarak düşünebiliriz.
Gelen isteğe uygun yanıt controller ile kullancıya ulaştırılır.

controller oluşturmak için;
-rails g controller welcome index (welcome ismiyle bir controller oluşturduk.)

Daha sonrasında bu komutu .alıştırdıktan sonra config>routes.rb altında
-root 'welcome#index'
olarak güncellemeliyiz.

# CRUD İşlemleri (Create-Read,Update-Delete)
Crud işlemlerine başlamadan önce,
-rails c 
crud moduna geçmek gerekiyor.


Create işlemi için şu iki yolu kullanabiliriz.

```
p=Post.new
p.title='Postun içeriği'
p.msg='Mesaj içeriği'
p.save
````
yada 
```
Post.create(title: 'Başlık',msg:'içerik')
```
Read işlemini yaparken tekrar "-rails c" demeyi unutmamalıyız. Bu işlem için aşağıdaki filtreleme 
işlemlerinden birini kullanabiliriz.

```
p=Post.first
p=Post.last
p=Post.find(1)
p=Post.find_by(title:'1')
p=Post.where("title LIKE?","%Başlığı%")

```
Update işlemini ise aşağıdaki gibi gerçekleştirebiliriz.
```
p=Post.first
p.title="başlık"
p.save (böylece ilk kaydın başlığını değiştirmiş olduk.)
```

Delete işlemi için;
```
p=Post.least
p.destroy
```
Eğer tüm kayıtları silmek istiyorsak;
```
Post.destroy_all
```
## Bir Rails Projesinde Dosya Dizinleri ve Anlamları

* app/: Views,controllers,model katmanları bulunur.
* config/: Veritabanı ile ilgili dosyalar bulunur.
* db/: Geçerli olan veritabanı şeması ve veritabanı geçişleri için(Migration) içerir.
* Gemfile: Rails uygulamasında mevcut olan Gemleri belirtir.
* log/: Log dosyalarının bulunduğu yerdir.
* public/: Resimler, js dosyaları ve stiller bulunur.
* tmp/: Geçici dosyaları bulundurur.

## Scaffold Yapısı

Rails'de  uzun uzun kod yazmak yerine basit bir model,view ve
controllerın otomatik bir şekilde oluşturulmasını ifade eder.
```
rails g scaffold books (komutu ile CRUD işlemleri otomatik olarak oluşturulur.)
```

## CRUD İŞLEMLERİ API
Öncelikle model ve controller oluşturalım.

```
class UsersController < ApplicationController

before_action :set_user, only: [:show, :update, :destroy]

  # GET /users
  def index
    @users = User.all
    render json: @users

  end

  # GET /users/:id
  def show
    render json: @user
  end

  # POST /users
  def create
    @user = User.new(user_params)

    if @user.save
      render json: @user, status: :created, location: @user
    else
      render json: @user.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT /users/1
  def update
    if @user.update(user_params)
      render json: @user
    else
      render json: @user.errors, status: :unprocessable_entity
    end
  end

  # DELETE /users/1
  def destroy
    @user.destroy
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_user
      @user = User.find(params[:id])
    end

    # Only allow a trusted parameter "white list" through.
    def user_params
      params.require(:user).permit(:email, :password, :password_confirmation)
    end
end
```

Model yapımız için ise;
```
class User < ApplicationRecord
  validates :password, length: { minimum: 8 }
  validates :password, confirmation: true
  validates :password_confirmation, presence: true

  validates :email, presence: true, uniqueness: true
end

```
