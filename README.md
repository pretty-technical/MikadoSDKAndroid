# Mikado SDK

## Content

- [Description](#description)

- [Architecture](#architecture)

    - [UseCase](#UseCase)
    - [Data](#data)
    - [Domain](#domain)
        - [Entities](#entity)
        - [Services](#service)
        - [Exception](#exception)
        - [Mapper](#mapper)
    - [Core](#core)

- [Features](#features)

    - [Login](#Login)

    - [SignUp](#SignUp)

    - [Game List](#Game-List)

    - [Game Launch](#Game-Launch)

    - [Balance Amount](#Balance-Amount)

- [External Library](#external-library)



## Description 

MikadoSDK is a software development kit (SDK) for Android gaming apps that implement main features as login, sign up, get the game list and launch games. The SDK simplifies the development of new gaming apps and manage all the backend connections.   

## Install

First, add the following lines in your build.gradle(app):
```
maven {
    url  "https://dl.bintray.com/pretty-technical/MikadoSDKAndroid"
}
```

To install the SDK you just add on your `build.gradle` file the following: 
- `implementation 'com.prettytechnical:mikado-sdk:0.0.6'`
- `implementation "org.koin:koin-core:2.1.6"`

Finally, sync.

## Usage

Mikado SDK is really simple to setup in the project. In the `MainApplication` declare `Mikado.instance.initialize(context: Context, api: ApiEnvironment)` which will automatically validate if the user's session has expired and get the currency list. The `initialize(context: Context, api: ApiEnvironment)` method need as a parameter the API environment, which are:
- `ApiEnvironment.PROD`
- `ApiEnvironment.DEV`
- `ApiEnvironment.STG`

The SDK also has other methods that can be implemented inside the project, these are:

- `Mikado.instance.hasSession()`
- `Mikado.instance.downloadGameList()`

Also, the SDK have a group of use cases for easy access, these are:

- `Mikado.instance.player`
- `Mikado.instance.arcade`

Every method used, except `initialize`, needs to be wrap in a Coroutine Scope. For more information about Coroutine, check the external library section. 

## Architecture
The project is base on Clean Architecture, it includes the Use Case layer, Data layer and Domain Layer. Additionally, there is a Core layer where all the base implementations are.  

### UseCase

- GameUseCase
- UserUseCase

### Data
- APIStatusCode
- DataDomainMapper
- DataRequest
- PreferenceData
- Repository

### Domain

#### Entities
- GameEntity
- LaunchGameEntity
- LogInEntity
- SignUpEntity

#### Services
- GameService
- LogInService
- SignUpService

#### Exception
- CreateURLException
- InternalException
- InvalidCredentialsException
- NotFoundException
- ServerException

#### Mapper
- DomainUseCaseMapper

### Core

- BaseEntity
- BaseError
- BaseService
- Constants
- SDKDelegate

## Features

### Login

The login module gets and validates the user's credencials and return the user profile information and access token or an exceptions if the credentials are wrong. 

To implement login in your project, just with `Mikado.instance.player.logIn(email: String, password: String, result: (Throwable) -> Unit)` where it just need the email and password input by the user. To handle the exceptions, is necessary to execute the result, for example:
``` Java
{ result ->
    try {
        throw result
    } catch (ex: Throwable) {
        print(ex)
    }
}
```
#### Exceptions

The login method can return the following exceptions:

- `internalError`
- `invalidCredentials`

#### Success Result

When the login is successful, the method will:

- Save the email, password, token, user Id in the `Preference`  
- Set the user information in the observable `userInformation`, which to be use in the app, you should called as:
``` Java
Mikado.instance.player.onUserInformationChanged = { userInformation ->
    //ADD YOUR LOGIC HERE
}
```
- Call the service of get the game list. 

### SignUp

The sign up module gets the user information and return the user profile information and access token or an exceptions if something went wrong. 

To implement the sign up in your project, just with `Mikado.instance.player.signUp(signUpParam: SignUpParam, result: (Throwable) -> Unit)` where it just need the user information as `SignUpParam` model. To handle the exceptions, is necessary to execute the result, for example:
``` Java
{ result ->
    try {
        throw result
    } catch (ex: Throwable) {
        print(ex)
    }
}
```
The `SignUpParam` model have the following attributes:

- companyID
- firstName
- lastName 
- dateOfBirth
- contactNumber 
- country 
- address 
- city
- postCode 
- email
- password 
- currencyID
- offerEmail 
- emailVerified 
- idVerified


#### Exceptions

The sign up method can return the following exceptions:

- `internalError`
- `invalidCredentials`

#### Success Result

When the sign up is successful, the method will call the login method with the email and password input of the user.

### Game List

The game list module is for get the list of games available to the user. 

To implement the game list in your project, just with `Mikado.instance.arcade.getGameList(result: (Throwable) -> Unit)` where it does not request any parameters. To handle the exceptions, is necessary to execute the result, for example:
``` Java
{ result ->
    try {
        throw result
    } catch (ex: Throwable) {
        print(ex)
    }
}
```

#### Exceptions

The game list method can return the following exceptions:

- `internalError`
- `invalidCredentials`

#### Success Result

When the game list is successful, the method will save the list in a observable that can be called as:
``` Java
Mikado.instance.arcade.onGameListChanged = { gameList ->
    //ADD YOUR LOGIC HERE
}
``` 

### Game Launch
The game launch module is used to launch the game the user select. 

To implement the launch game in your project, just with `Mikado.instance.arcade.launchGame(gameId: String?, providerId: Int?, provideSlug: String?, result: (Throwable) -> Unit?)` where it just need the game Id, provider Id and provide Slug input of the game. To handle the exceptions, is necessary to execute the result, for example:
``` Java
{ result ->
    try {
        throw result
    } catch (ex: Throwable) {
        print(ex)
    }
}
```
#### Exceptions

The launch game method could return the following exceptions:

- `internalError`
- `invalidCredentials`

#### Success Result

When the launch game is successful, it will return the game url to access it in a observable that can be called as:
``` Java
Mikado.instance.arcade.onGameURLChanged = { gameURL ->
    //ADD YOUR LOGIC HERE
}
```
You should use a  WebView to lauch the game.

### Balance Amount
The balance amount module is used to get the user amount of points. 

To implement the launch game in your project, just with `Mikado.instance.player.getBalance(result: (Throwable) -> Unit?)`. To handle the exceptions, is necessary to execute the result, for example:
``` Java
{ result ->
    try {
        throw result
    } catch (ex: Throwable) {
        print(ex)
    }
}
```
#### Exceptions

The balance method could return the following exceptions:

- `internalError`
- `invalidCredentials`

#### Success Result

When getting the balance is successful, it will return the amount where it can be access by an observable, which can be called as:
``` Java
Mikado.instance.player.onBalanceChanged = { balance ->
    //ADD YOUR LOGIC HERE
}
```
## External Library

| Plugin | Version | Site |
| ------ | ------- | ---- |
| Retrofit | 2.9.0 | https://square.github.io/retrofit/ |
| Koin | 2.1.6 | https://github.com/InsertKoinIO/koin |
| Coroutine| 1.3.9 | https://kotlinlang.org/docs/reference/coroutines/coroutines-guide.html|
