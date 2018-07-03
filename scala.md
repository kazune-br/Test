#### 関数リテラル
```
// res0: Int => Int = Function1
(x: Int) => x + 1  

// test: (Int, Int) = Int = Function2
var test = (x: Int, y: Int) => x + y 

// test: Int => Int = Function1
var test = (x: Int) => {
    println("test")
    x + 1
}

```

#### PlaceHolder
```
val lst = List(1, 2, 3, 4, 5)
lst.foreach(x => println(x))
lst.foreach(println _)
lst.filter(_ > 0)
```
<br>

#### Tuple Basic
```
(1, 2, 3)._1 // 1
("A", "B", "C")._2 // B
```
<br>

#### Tuple, Future, caseの挙動の確認 
```
import scala.collection.mutable
import scala.concurrent.{ExecutionContext, Future}
import ExecutionContext.Implicits.global

class User(val userId: Int)

val users: mutable.HashMap[String, User] = { mutable.HashMap() }
users += ("key" -> new User(1))
users.get("key") // Option[User] = Some(User(1))

def find(key: String) = Future.successful(users.get(key))
find("key") // scala.concurrent.Future[Option[User]] = Future(Success(Some(User(1)))
find("not key" ) // scala.concurrent.Future[Option[User]] = Future(Success(None)))

val checkId = 1
users.find { case (_, user) => user.userId == checkId } // Option[(String, User)] = Some(key, User(1))
users.find { case (_, user) => user.userId  == checkId }.map(_._1) // Option[String] = Some(key)
users.find { case (_, user) => user.userId == checkId }.map(_._2) // Option[User] = Some(User(1))

// find: (dummy: String, checkID: Int)scala.concurrent.Future[Option[User]]
def find(dummy: String, checkID: Int) = Future.successful(
    users.find { case (_, user) => user.userId == checkID }.map(_._2)
)

// scala.concurrent.Future[Option[User]] = Future(Success(Some(User(1))))
find("tmp", 1) 

// scala.concurrent.Future[Object] = Future(Success(User(1)))
find("tmp", 1).flatMap { case Some(user) => Future.successful(user) case None => Future.successful("BAD") }

// error: value userId is not a member of Option[User]
// users.get("key").userId

// User = User(1)
users.get("key").get

// Object = User@6584df56
users.get("key2").getOrElse("MECE")
```
