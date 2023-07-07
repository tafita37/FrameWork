Prérequis/Technologie utilisées :
    - java et jdk8
    - tomcat8
    - postgresql10
    - Librairie : gson-2.8.2.jar (pour convertir des objets en json)

NB : La version de java et de postgres ne devrait pas poser de problème à condition que vous téléchargiez les librairies nécessaire au connexion base
     La librairire gson-2.8.2.jar est obligatoire même si vous ne prévoyez pas de vous en servir dans le développement de votre application

Deploiement :
    - Veuillez configurer le fichier web.xml dans le dossier WEB-INF de la façon suivante   :
        <?xml version="1.0" encoding="UTF-8"?>
        <web-app xmlns="http://java.sun.com/xml/ns/j2ee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
            http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
            version="2.4">
            <servlet> 
                <servlet-name>Front</servlet-name> 
                <servlet-class>ETU1863.framework.servlet.FrontServlet</servlet-class> 
                <init-param>
                    <param-name>paquet</param-name>
                    <param-value> <<nom du dossier contenant toutes vos class a partir du dossier WEB-INF>> </param-value>
                </init-param>
                <init-param>
                    <param-name>role</param-name>
                    <param-value> <<Le nom du role qui permet de connaitre les autorisations>> </param-value>
                </init-param>
                <init-param>
                    <param-name>profil</param-name>
                    <param-value> <<La cle qui permet de savoir si un utilisateur est connecte>> </param-value>
                </init-param>
            </servlet>
            <servlet-mapping> 
                <servlet-name>Front</servlet-name>
                <url-pattern>*.do</url-pattern>
            </servlet-mapping>
        </web-app>

        NB : les init-param role profil doivent etre defini obligatoirement pour gérer les autorisations
             Chaque url devra contenir l'extension .do nous verrons cela par la suite
    
    - Placer les librairies gson-2.8.2.jar et Framework.jar dans le dossier lib de votre projet ou celui de tomcat(de préférence)

Contraintes d'utilisation :
    - Créer des class qui vous serviront de controller et annoter les avec l'annotation @Url(link=<< url associe a la fonction (extension.do) >>)
        NB : Si votre fonction annoté @Url n'est pas annoté @Json elle doit impérativement retourné une ModelView
    - Si vous souhaitez charger une vue instancier une ModelView à l'aide du constructeurs qui prend en argument un tableau de String(vues) à charger
    - Si vous souhaitez envoyer des données de votre controller vers votre vue utilisateur la méthode addItem de votre ModelView et récupérer et caster le dans la vue à l'aide de request.getAttribute()
    - Pour envoyer des données de votre vue (variable de lien ou de formulaire) vers votre controller il y a deux possibilités :
        1) Mettre en attribut du controller les noms de chaque variable de votr vue (le cast est automatique)
        2) Mettre ces variables en arguments et utiliser l'annotation @Parameter(args={ << nom des variables de liens >> })
            NB : l'ordre des noms de variables dans l'annotation @Parameter et celle des parametres de votre method doivent impérativement correspondre
    - Si vous souhaitez uploader un fichier rajouter un attribut de type FileUpload dans votre controller et utiliser le contructeur FileUpload(String) pour définir l'emplacement ou ira le fichier 
    - Au cours du développement de votre application il se pourrait que vous souhaitez ne plus avoir à réinstancier une même classe indéfiniment à chaque appel de fonction pour économiser en ressource et en espace mémoire alors utiliser l'annotation @Scope(singleton=true)
    - Pour rajouter de nouvelle variables de sessions utiliser la méthode addSession(String, Object) de l'objet ModelView 
    - Pour gérer les autorisations utilisez l'annotation @Auth(role=<<role autorisé>>). Cela va récupérer la valeur du role dans init-param role et va vérifier si une session a déjà été définie et si cela correspond à ce qu'il y a dans @Auth et vérifier si en session il y a la valeur de variable profil en init-param et si elle est égale à true. Si c'est le cas alors l'utilisateur sera autorisé à appeler la fonction. Dans @Auth il y a un attribut String exception(), définissez les raisons qui font que seule l'utilisateur défini peut utiliser la fonction.
    - Dans votre controller, si vous souhaitez acceder au variables de sessions rajouter un attribut de type HashMap<String, Object> et annoter le avec l'annotation @SessionField. Vous aurez automatiquement accés à toutes les fonctions dont vous aurez besoin.
    - Si vous souhaitez effacer une variable de session utiliser la méthode removeSession(String, Object) du ModelView. Si vous souhaitez supprimer toutes les sessions utiliser la setInvalidateSession(false) de ModelView.
    - Si vous souhaitez convertir les datas de votre ModelView utilisez setJson(true) de la méthode ModelView
    - Annotez votre méthode avec l'annotation @Json et le type de retour sera converti en objet Json


A l'ouverture de l'application :
    - Veuillez créer un fichier index.html ou index.jsp et faites la commande suivante
        <% response.sendRedirect(<<url de redirection de votre choix>>); %>