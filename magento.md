# Formation Magento

Utile sur phpStorm
CTRL + ESPACE = pour le préremplissage class, template ect
CTRL + SHIFT + A = rechercher une action
SHIFT F6 = rename


## La structure de base
Dans un module magento il y a __2 parties__, le __« code »__ et les __« templates »__.      

```
/app/code/local/monNameSpace/monNomDeModule/
/app/design/frontend/monRepertoiredeTemplates/monTemplate  //(pour la partie frontend)
/app/design/adminhtml/monRepertoiredeTemplates/monTemplate //(pour la partie backend) 
```


## Le dossier /app/ et les codePool magento
Dans le dossier __app/__ vous retrouverez 3 dossiers (codePool) qui contiendront :     

__code__ : avec tous le code de l'aplication
	__community__ : les plugins de la communauté (téléchargés sur magentoconnect par exemple).    
	__core__ : les modules par défaut de magento (A NE SURTOUT PAS MODIFIER)     
	__local__ : les modules que vous avez développés vous même     
__design__ : avec les vues de l'application
	__adminhtml__ : les vues de l'admin    
	__frontend__ : les vues front     


## la structure d'un module magento
__Block__ : la ou vous aller mettre la partie « controller » de votre block       
__controllers__ : les controllers de votre module        
__Model__ : les models de votre module      
__Helper__ : les helpers de votre module       
__etc__ : la configuration de votre module       
__sql__ : les requettes sql a executé pour créer votre module lors de l’instalation       
        

_Petit rappel sur les blocks_     
Dans Magento, un Block est un morceau de votre site qui marche « tout seul », vous pouvez l’insérer un peu n’importe où. Selon la page de votre site, vous pourrez lui assignez des templates différents en fonction de l'affichage que vous voulez lui donner.


## Créez votre Controller
1. Créer le dossier dans `/app/code/local/NameSpace/NameModule/`     
2. Activez le module dans magento : Ajoutez un fichier __NameSpace_NameModule.xml__ dans `/app/etc/module/` Et insérez dedans le code suivant     
      
```xml
   <config>
      <modules>
        <NameSpace_NameModule>
            <active>true</active>
            <codePool>local</codePool>
         </NameSpace_Module>
       </modules>
    </config>
```

Magento trouvera donc les fichiers de ce module dans le dossier `/app/code/local/NameSpace/NameModule/`     

3. Créez le dossier `/app/code/local/NameSpace/NameModule/controllers/`      
4. A l’intérieur créer un fichier __IndexController.php__ qui contiendra :      

```php
class NameSpace_NameModule_IndexController extends Mage_Core_Controller_Front_Action
{
   public function indexAction()
   {
     echo 'test index' ;
   }

   public function mamethodeAction()
   {
     echo 'test mamethode';
    }
}
```

5. __Config.xml__, le fichier pour configurer votre module :    
il faut que dans le fichier config.xml de votre module, vous déclariez aussi votre controller . Mangento va ainsi trouver votre fichier NameSpace_NameModule.xml, il va aller dans votre dossier `/app/code/local/NameSpace/NameModule/etc/` et va lire le fichier __config.xml__ pour savoir ou aller chercher votre controlleur et comment fonctionne votre module. 

``xml
<config>
     <modules>
        <NameSpace_NameModule>
          <version>1.0.0</version>
        </NameSpace_NameModule>
     </modules>
     <frontend>
       <routers>
          <routeurfrontend>
              <use>standard</use>
              <args>
                 <module>NameSpace_NameModule</module>
                 <frontName>Rewriting</frontName>
              </args>
           </routeurfrontend>
       </routers>
    </frontend>
</config>
```

## Créer un block

#### Etape 1 : Appeler le layout dans le controller

Modifier le controller avec __loadLayout()__ et __renderLayout()__     

```php
class NameSpace_NameMdule_IndexController extends Mage_Core_Controller_Front_Action
{
     public function indexAction()
     {
          $this->loadLayout();    // Va chercher les elements à afficher
          $this->renderLayout();  // Affiche les elements
     }

      public function mamethodeAction()
      {
          echo ‘test mamethode’;
      }
}
```
   
###  Etape 2 : Déclarer les éléments à afficher grâce au Layout.
Avant de pouvoir utiliser le Layout on doit le déclarer. Pour cela allons dans notre fichier __config.xml__ rajoutez dans __<frontend>__ et apres __<routers>__ :

```xml
<layout>
   <updates>
        <nameSpace_moduleName>
             <file>nameSpace_nameModule.xml</file>
         </nameSpace_moduleName>
    </updates>
</layout>
```

Maintenant, déclarons le dossier ou vont se trouver les Blocks : Dans __<config>__ et âpres __<frontend>__ rajoutez 

```xml
<global>
     <blocks>
         <nameSpace_moduleName>
              <class>NameSpace_NameModule_Block</class>
         </nameSpace_moduleName>
      </blocks>
</global>
```

Dans __<global>__ vous déclarez vos Blocks, c’est aussi ici que vous déclarerez par la suite vos Model et vos Helpers.     
      
__Le layout__ : nous avons déclaré dans le config.xml de notre module un layout nameSpace_nameModule.xml dans le repertoire layout de votre théme Magento `/app/design/frontend/NameSpace/theme/layout/`      
Nous allons donc créer un Layout nameSpace_nameModule.xml dans le répertoire     

```xml
<layout version="0.1.0">
     <default>
          <reference name="content">
          </reference>
      </default>

      <routeurfrontend_index_index>
           <reference name="content">
                <block type="nameSpace_nameModule/monblock"  name="monbloc" template="nameSpace_nameModule/afficher.phtml" />
           </reference>
      </routeurfrontend_index_index>
</layout>
```

### Etape 3 : Créer le block
Maintenant, il faut créer le block « monblock», allez donc dans le dossier que vous avez défini dans le config.xml pour stocker vos blocks `app/code/local/NameSpace/NameModule/Block` et créer le fichier __Monblock.php__ à l'intérieur.       
      
```php
class NameSpace_NameModule_Block_Monblock extends Mage_Core_Block_Template
{
     public function methodblock()
     {
         return ‘informations de mon block !!’ ;
     }
}
```

###n Etape 4 : Créer le template
Allez maintenant dans le répertoire `\app\design\frontend\pfay\theme\template\` Et créer un répertoire __"nameSapce_nameModule"__ avec à l’intérieur un fichier __afficher.phtml__. 

```
    echo $this->methodblock();
```
 
## Le Model et la base de donnée

### Etape 1 : Créer la table

### Etape 2 : Déclarer le Model dans config.xml
déclarer le model dans le fichier __config.xml__   dans __<global>__ :      

```xml
<models>
    <pfay_films>
         <class>Pfay_Films_Model</class>
         <resourceModel>pfay_films_resource</resourceModel>
     </pfay_films>
    <pfay_films_resource>
         <class>Pfay_Films_Model_Resource</class>
         <entities>
             <film>
               <table>pfay_films</table>
             </film>
          </entities>
    </pfay_films_resource>
</models>
<resources>
        <!-- connection pour ecrire -->
        <pfay_films_write>
            <connection>
                <use>core_write</use>
            </connection>
        </pfay_films_write>
        <!-- connection pour lire-->
       <pfay_films_read>
          <connection>
             <use>core_read</use>
          </connection>
       </pfay_films_read>
</resources>
```

### Etape 3 : Créer la classe du Model
Une fois votre Model déclaré, vous pouvez maintenant créer les fichiers.    
       
Créez donc les 3 dossiers suivant :      
- app/code/local/Pfay/Films/Model/      
- app/code/local/Pfay/Films/Model/Mysql4/        
- app/code/local/Pfay/Films/Model/Mysql4/Films/ Ensuite, dans app/code/local/Pfay/Films/Model/ créez un fichier Film.php qui contiendra le code suivant :       

```xml
class Pfay_Films_Model_Film extends Mage_Core_Model_Abstract
{
     public function _construct()
     {
         parent::_construct();
         $this->_init('pfay_films/film');
     }
}
```

Ceci est votre model Film, vous lui indiquez que c’est une entitée film de votre module pfay_films. Allez ensuite dans le dossier app/code/local/Pfay/Films/Model/Resource/ et créez un fichier Film.php qui contiendra :    

```php
class Pfay_Films_Model_Resource_Film extends Mage_Core_Model_Resource_Db_Abstract
{
     public function _construct()
     {
         $this->_init('pfay_films/film', 'id_pfay_films');
     }
}
```

C’est ici que vous indiquez à Magento que votre model pfay_films/film va utiliser comme clef primaire le champ id_pfay_films.       

Allez ensuite dans le dossier app/code/local/Pfay/Films/Model/Resource/Film/ et créez un fichier Collection.php qui contiendra :      

```php
class Pfay_Films_Model_Resource_Film_Collection extends Mage_Core_Model_Resource_Db_Collection_Abstract
 {
     public function _construct()
     {
         parent::_construct();
         $this->_init('pfay_films/film');
     }
}
```

Ce fichier sert à définir la collection pour votre model pfay_films/film      
Une fois ces fichiers créés, vous pouvez utiliser votre Model pour interagir avec vos tables très facilement.     
       
        
### Etape 4 : Lister les entités de la table de données dans Block


```php
class Pfay_Films_Block_Monblock extends Mage_Core_Block_Template
{
     public function methodblock()
     {
        //on initialize la variable
        $retour='';

        /* on fait une requette : aller chercher Tous les elements
        de la table pfay_films (grace à notre model pfay_films/pfay_film
        et les trier par id_pfay_films */

        $collection = Mage::getModel('pfay_films/pfay_film')
                            ->getCollection()
                            ->setOrder('id_pfay_films','asc');

         /* ensuite on parcours le resultat de la requette et
          avec la fonction getData(), on stocke dans la variable retour
          (pour l’affichage dans le template) les données voulues */

        foreach($collection as $data)
        {

             $retour .= $data->getData('name.').'<br />';

         }

         //je renvoi un message de succes à l'utilisateur (juste pour que vous sachiez utiliser la fonction)

         Mage::getSingleton('adminhtml/session')->addSuccess('Cool Ca marche !!');

         return $retour;
      }
 }
 ```






