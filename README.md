# Descripción de la aplicación

Nosotros lo que hicimos fue fusionar los microservicios de Auth, Account con uno que creamos nuevo (Venta y Compra de Divisa USD - Billetera Virtual/Forex).

En la fusión, lo que hicimos fue que al momento de que el usuario/cliente del banco, compre USD con COP, se valida que en su cuenta bancaria tenga $. En caso que tenga dinero, al hacer la compra de USD, los $ se le descuentan al balance de la cuenta bancaria y a la billetera virtual se le agregan los USD.
Para el caso de la venta, lo que hace el sistema es validar que el usuario tenga en su billetera virtual la cantidad de USD a vender. Si esto es verdadero, se descuentan los USD de la billetera virtual y en la cuenta bancaria se le suman los $.

Hay que tener en cuenta que el banco maneja una tasa de cambio de USD y una comisión que es tenida en cuenta para la venta y la compra de la divisa USD.

# Para ejecutar la app en local desde VS code

```
npm install
npm run serve
```

# Para desplegar la app en Heroku

```
heroku login
```

Se inicia sesion en la web de Heroku

```
heroku create nombre_app
heroku container:login
heroku container:push web -a nombre_app
heroku container:release web -a nombre_app
heroku open -a nombre_app
```
# Para mas información
Por favor visitar: https://github.com/raparram/C4P65G2_DW_4a-docs

