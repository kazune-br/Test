# Play2.6 Upload Image 準備
> Ref:
- https://www.playframework.com/documentation/2.6.x/ScalaFileUpload
- https://github.com/playframework/play-scala-fileupload-example
- https://www.playframework.com/documentation/2.6.x/ScalaForms
- https://www.playframework.com/documentation/2.6.x/api/scala/index.html#play.api.data.Form
- https://github.com/playframework/play-scala-fileupload-example/issues/50
- https://blog.knoldus.com/2013/01/22/play-framework-uploading-a-file-to-server/
- https://alvinalexander.com/source-code/scala/scala-how-get-current-directory-scala-application
<br>

## Controllerの準備
ex: controllers/ImageUploadController.scala
```
package controllers

import java.nio.file.Paths
import javax.inject._
import play.api.mvc._
import scala.concurrent.ExecutionContext

class ImageUploadController@Inject()(cc: MessagesControllerComponents)(implicit ec: ExecutionContext)
  extends MessagesAbstractController(cc) {

  def index = Action { implicit request =>
    Ok(views.html.index())
  }
  
  def ret = Action { implicit request =>
    Ok(views.html.ret())
  }

  def upload = Action(parse.multipartFormData) { implicit request =>
    request.body.file("image").map { image =>
      val filename = Paths.get(image.filename).getFileName
      image.ref.moveTo(Paths.get(s"./public/uploaded_pictures/$filename"), replace = true)
      Redirect(routes.ImageUploadController.empty).flashing("success" -> "Uploading file is completed")
    }.getOrElse {
      Redirect(routes.ImageUpload.empty).flashing("error" -> "Missing file")
    }
  }
}
```
<br>

## Viewの準備
ex: views/upload/index
```
@()(implicit request: MessagesRequestHeader)
@import helper._

@main("Welcome to Play") {
    @form(action = routes.ImageUploadController.upload, 'enctype -> "multipart/form-data") {
        <input type="file" name="image">
        @CSRF.formField
        <div class="buttons">
            <input type="submit" value="Upload"/>
        </div>
    }
}
```
<br>

## Routes
ex: conf/routes
```
GET  /index controllers.ImageUploadController.index
GET  /ret controllers.ImageUploadController.ret
POST /upload controllers.ImageUploadController.upload
```
<br>

## Confの変更
conf/application.conf
```
play.http.parser.maxMemoryBuffer= 10MB  # Defaultでは128KB => サイズを超過するとCSRF系のエラーが発生する

```

