# Mode historique de HTML5

Le mode par défaut de `vue-router` est le _mode hashe_. Il utilise la partie hash de l'URL pour simuler une URL complète et ainsi ne pas recharger la page quand l'URL change.

Pour se passer du hash, nous pouvons prendre en main le **mode historique** qui va utiliser l'API `history.pushState` pour réaliser de la navigation d'URL sans recharger la page :

``` js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

Quand vous utilisez le mode historique l'URL ressemblera a n'importe quelle URL normale. Par ex. `http://oursite.com/user/id`. Magnifique !

Cependant, un problème apparaît ici. Si votre application est une application monopage cliente, sans une configuration serveur adaptée, les utilisateurs tomberons sur une page d'erreur 404 en tantant d'accéder à `http://oursite.com/user/id` directement dans leur navigateur. Maintenant ça craint.

<<<<<<< HEAD
Ne vous inquiétez pas : pour résoudre ce problème, il vous suffit d'ajouter une route à votre serveur prenant en compte toutes les adresses demandées. Si l'URL demandé ne concorde avec aucun fichier statique, alors il doit toujours renvoyer la page `index.html` où votre application doit se lancer. De nouveau magnifique !
=======
Not to worry: To fix the issue, all you need to do is add a simple catch-all fallback route to your server. If the URL doesn't match any static assets, it should serve the same `index.html` page that your app lives in. Beautiful, again!
>>>>>>> working

## Exemple de configurations serveur

#### Apache

```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.html [L]
</IfModule>
```

#### nginx

```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

#### Node.js natif

```js
var http = require("http"),
  fs = require("fs"),
  httpPort = 80;

http.createServer(function (req, res) {
  fs.readFile("index.htm", "utf-8", function (err, content) {
    if (err) {
      console.log('We cannot open "index.htm" view file.');
    }

    res.writeHead(200, {
      "Content-Type": "text/html; charset=utf-8"
    });

    res.end(content);
  });
}).listen(httpPort, function () {
  console.log("Server listening on: http://localhost:%s", httpPort);
});
```

#### Node.js avec Express

Pour Node.js / Express, vous pouvez utiliser le [middleware connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback).

#### Internet Information Services (IIS)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Handle History Mode and custom 404/500" stopProcessing="true">
            <match url="(.*)" />
            <conditions logicalGrouping="MatchAll">
              <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
              <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
            </conditions>
          <action type="Rewrite" url="index.html" />
        </rule>
      </rules>
    </rewrite>
      <httpErrors>
          <remove statusCode="404" subStatusCode="-1" />
          <remove statusCode="500" subStatusCode="-1" />
          <error statusCode="404" path="/survey/notfound" responseMode="ExecuteURL" />
          <error statusCode="500" path="/survey/error" responseMode="ExecuteURL" />
      </httpErrors>
      <modules runAllManagedModulesForAllRequests="true"/>
  </system.webServer>
</configuration>
```

#### Caddy

```
rewrite {
    regexp .*
    to {path} /
}
```

## Limitation

Il y a une limitation a ceci : votre serveur ne renverra plus les erreurs 404 des chemins qui ne sont pas trouvés puisqu'il va servir à présent le fichier `index.html`. Pour contourner ce problème, vous pouvez implémenter une route concordant avec toutes les adresses en 404 dans votre application Vue :

``` js
const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '*', component: NotFoundComponent }
  ]
})
```

Une arternative possible, si vous utilisez un serveur Node.js, est d'implémenter ce mécansisme de substitution en utilisant le routeur côté serveur pour vérifier la concordance des demande d'URL entrante qui correspondrait à une route inexistante. Consultez l'[utilisation de Vue côté serveur](https://ssr.vuejs.org/en/) pour plus d'informations.
