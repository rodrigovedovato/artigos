## Anteriormente

Na [parte 1](/injecao-de-dependencia-em-scala-parte-1), vimos como resolver o problema da Injeção de Dependência em Scala utilizando  o Guice, framework feito pelo Google. Até o momento, nosso código está assim:

```scala
case class UserInfo(account: String, password: String)

trait UserService {
    def getUserInfo(account: String): UserInfo
}

class UserServiceComponent extends UserService {
    override def getUserInfo(account: String): UserInfo = UserInfo(account, "123")
}

trait  LoginFacadeT {
  def  login(account: String, password: String):  Boolean
}

class LoginFacade @Inject()(userService: UserService) extends LoginFacadeT {
    override def login(account: String, password: String): Boolean = {
        userService.getUserInfo(account).password == password
    }
}

class HomeController(facade: LoginFacadeT) {
  def autenticarUsuario(usuario: String, senha: String): String = {
    if (facade.login(usuario, senha)) {
      s"Olá, $usuario"
    } else {
      "Erro de login"
    }
  }
}

class DependencyInjectionWithGuiceModule extends AbstractModule {
    override def configure(): Unit = {
        bind(classOf[UserService]).to(classOf[BasicUserService])
        bind(classOf[LoginFacadeT]).to(classOf[LoginFacade])
    }
}

object DependencyInjectionWithGuice extends App {
    val injector = Guice.createInjector(new DependencyInjectionWithGuiceModule())
    lazy val loginFacade: LoginFacadeT = injector.getInstance(classOf[LoginFacadeT])

    val homeController = new HomeController(loginFacade)
    println(homeController.authenticateUser("Rodrigo", "123"))
}
```
Funciona corretamente, mas 
<!--stackedit_data:
eyJoaXN0b3J5IjpbODU0NDQwMTE3LC0yMDg4NzQ2NjEyLC0zMz
I0NTUzNjNdfQ==
-->