# CryptoZombies

## Table of contents

1. [Presentation](#presentation)
2. [Memento](#memento)
3. [Backend](#----------------BACKEND----------------)
4. [Frontend](#----------------FRONTEND----------------)

## Presentation

>This is the cryptozombies dapp tutoriel source code from [cryptozombies](https://www.cryptozombies.io). I'm also trying to keep the useful informations from that tutorial and what makes solidity and dapps amazing.

## Memento

### Backend

- Demander confirmation avant d'executer le code qui suit
`require(condition)`
- Recuperer l'id de la personne executant le smart contract
`msg.sender`
- Storage permet de modifier un element sur la blockchain
`Sandwich storage mySandwich = sandwiches[index];`
- Memory permet de recuperer un element sur la blockchain en le copiant par exemple
`Sandwich memory anotherSandwich = sandwiches[index];`
- Le mapping 
`mapping (uint => address) public zombieToOwner;`<br>
`mapping (address => uint) ownerZombieCount;`
- Exemple utilisation du mapping<br>
`zombieToOwner[id] = msg.sender;`<br>
`ownerZombieCount[msg.sender]++;`
- Declarer une fonction privée mais accessible par les contrats héritant du contrat ou est cette fonction
`function f() internal {}`
- Declarer une fonction public mais accessible seulement à l'exterieur du contrat
`function f() external {}`
- Declarer une fonction qui regarde sans modifier
`function f() view {}`
- Declarer une fonction qui n'a aucun side-effect
`function f() pure {}`
- Exemple d'interface
`contract NumberInterface {function getNum(address _myAddress) public view returns (uint);}`
- Initialisation d'une interface KittyInterface grace a l'adresse du smart contract<br>
`address ckAddress = 0x06012c8cf97BEaD5deAe237070F9587f8E7A266d;`<br>
`KittyInterface kittyContract = KittyInterface(ckAddress);`<br>
- Modificateurs de fonctions
`modifier olderThan(uint _age, uint _userId) {
 require (age[_userId] >= _age);
  _;
}`<br>

### Frontend

- Importer web3.min.js<br>
`<script src="https://cdn.jsdelivr.net/npm/web3@latest/dist/web3.min.js"></script>`<br>
- Utiliser infura<br>
`var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));`<br>
- Instancier web3<br>
`var myContract = new web3js.eth.Contract(myABI, myContractAddress);`<br>
- Call function<br>
`myContract.methods.myMethod(123).call()`<br>
- Send function<br>
`myContract.methods.myMethod(123).send()`<br>
- Demander les informations du contrat public<br>
`function getZombieDetails(id) {
  return cryptoZombies.methods.zombies(id).call()
}`<br>
- Afficher ses informations après reception<br>
`getZombieDetails(15)
.then(function(result) {
  console.log("Zombie 15: " + JSON.stringify(result));
});`<br>
- Recuperer l'adresse de l'utilisateur<br>
`var userAccount = web3.eth.accounts[0]`<br>
- Convertir ether en wei<br>
`web3js.utils.toWei("1", "ether");`<br>

------------------------------------------------------------------

## ----------------BACKEND---------------- 

------------------------------------------------------------------

## Gas

>En Solidity, vos utilisateurs devront payer à chaque fois qu'ils exécutent une fonction de votre DApp avec une monnaie appelée gas. Les utilisateurs achètent du gas avec de l'Ether (la monnaie d'Ethereum), vos utilisateurs doivent donc dépenser de l'ETH pour exécuter des fonctions de votre DApp.
La quantité de gas requit pour exécuter une fonction dépend de la complexité de cette fonction. Chaque opération individuelle à un coût en gas basé approximativement sur la quantité de ressources informatiques nécessaires pour effectuer l'opération (ex: écrire dans le storage est beaucoup plus cher que d'ajouter deux entiers). Le coût en gas total de votre fonction est la somme du coût de chaque opération individuelle.
Parce qu'exécuter des fonctions coûte de l'argent réel pour les utilisateurs, l'optimisation de code est encore plus importante en Solidity que pour les autres langages de programmation. Si votre code est négligé, vos utilisateurs devront payer plus cher pour exécuter vos fonctions - et cela pourrait résulter en des millions de dollars de frais inutiles répartis sur des milliers d'utilisateurs.
Ethereum est comme un ordinateur gros et lent, mais extrêmement sécurisé. Quand vous exécuter une fonction, chaque nœud du réseau doit exécuter la même fonction pour vérifier le résultat - c'est ces milliers de nœuds vérifiant chaque exécution de fonction qui rendent Ethereum décentralisé et les données immuables et résistantes à la censure.

## Optimisations

### --> Avec les uint :

>Il existe d'autres types de uint : uint8, uint16, uint32, etc.
Normalement, il n'y a pas d’intérêt à utiliser ces sous-types car Solidity réserve 256 bits de stockage indépendamment de la taille du uint. Par exemple, utiliser un uint8 à la place d'un uint (uint256) ne vous fera pas gagner de gas.

### --> Avec les structures :

>- Si vous avez plusieurs uint dans une structure, utiliser des plus petits uint quand c'est possible permettra à Solidity d'emboîter ces variables ensemble pour qu'elles prennent moins de place.<br>
`struct NormalStruct { uint a; uint b; uint c; }`<br>
`struct MiniStruct { uint32 a; uint32 b; uint c; }`<br>
>**MiniStruct** utilisera moins de gas que **NormalStruct** grâce à l’emboîtement de structure.
>- Vous pouvez passer un pointeur de stockage d'une structure comme argument à une fonction private ou internal. C'est pratique, par exemple, pour faire circuler notre structure Zombie entre fonctions. De cette manière, nous pouvons donner une référence à notre zombie à une fonction au lieu de donner l'Id zombie et le rechercher.

Remarque : Il sera aussi important de grouper les types de données (c.-à.-d. les mettre à coté dans la structure) afin que Solidity puisse minimiser le stockage nécessaire. Par exemple, une structure avec des champs uint c; uint32 a; uint32 b; coûtera moins cher qu'une structure avec les champs uint32 a; uint c; uint32 b; car les champs uint32 seront regroupés ensemble.

### --> Avec les vérifications public external :

>Si on regarde cette fonction, on peut voir que nous l'avions rendu public dans les leçons précédentes. Une habitude importante de sécurité et d'examiner tous les fonctions public et external, et essayer d'imaginer de quelles manières les utilisateurs pourraient en abuser. Rappelez-vous, à part si ces fonctions ont un modificateur onlyOwner, tout le monde peut appeler ces fonctions avec les données qu'il veut.
>La plus simple facon d'empêcher ce genre d'abus est de la rendre internal si possible.

### --> Avec les fonctions view :

>C'est parce que les fonctions view ne changent rien sur la blockchain - elles lisent seulement des données. Marquer une fonction avec view indique à web3.js qu'il a seulement besoin d'interroger votre nœud local d'Ethereum pour faire marcher la fonction, et il n'a pas besoin de créer une transaction sur la blockchain (qui devra être exécuter sur tous les nœuds et qui coûtera du gas).

Remarque : Si une fonction view est appelée intérieurement à partir d'une autre fonction du même contrat qui n'est pas une fonction view, elle coûtera du gas. C'est parce que l'autre fonction va créer une transaction sur Ethereum, et aura besoin d'être vérifiée par chaque nœud. Ainsi les fonctions view sont gratuites seulement quand elles sont appelées extérieurement.

### --> En évitant storage :

>Une des opérations les plus coûteuse en Solidity est d'utiliser storage - particulièrement quand on écrit.
C'est parce qu'à chaque fois que vous écrivez ou changez un bout d'information, c'est écrit de manière permanente sur la blockchain. Pour toujours ! Des milliers de nœuds à travers le monde vont stocker cette information sur leurs disques durs, et cette quantité d'information continue de grandir au fur et à mesure que la blockchain grandie. Et il y a un prix à cela.
Afin de réduire les coûts, vous voulez éviter d'écrire des données en stockage à part quand c'est absolument nécessaire. Par moment cela peut impliquer une logique de programmation qui à l'air inefficace - comme reconstruire un tableau dans la memory à chaque fois que la fonction est appelée au lieu de sauvegarder ce tableau comme une variable afin de le retrouver rapidement.
Dans la plupart des langages de programmation, faire une boucle sur un grand ensemble de données est coûteux. Mais en Solidity, c'est beaucoup moins cher que d'utiliser storage s'il y a une fonction external view, puisque view ne coûte aucun gas. (Et les gas coûte réellement de l'argent pour vos utilisateurs !).
Vous pouvez utiliser le mot clé memory avec des tableaux afin de créer un nouveau tableau dans une fonction sans avoir besoin de l'écrire dans le stockage. Le tableau existera seulement jusqu'à la fin de l'appel de la fonction, et cela sera beaucoup plus économique, d'un point de vue du gas, que de mettre à jour un tableau dans storage - c'est gratuit si c'est une fonction view appelée extérieurement.

## Time

>Solidity fourni nativement des unités pour gérer le temps.
La variable now (maintenant) va retourner l'horodatage actuel unix (le nombre seconde écoulées depuis le 1er janvier 1970). L'horodatage unix au moment où j'écris cette phrase est 1515527488.
Solidity a aussi des unités de temps seconds (secondes), minutes, hours (heures), days (jours) et years (ans). Ils vont se convertir en un uint correspondant au nombre de seconde de ce temps. Donc 1 minutes est 60, 1 hours est 3600 (60 secondes x 60 minutes), 1 days est 86400 (24 heures x 60 minutes x 60 seconds), etc.

Remarque : L'horodatage unix est traditionnellement stocké dans un nombre 32-bit. Cela mènera au problème "Année 2038", quand l'horodatage unix 32-bits aura débordé et cassera beaucoup de système existant. Si nous voulons que notre DApp continue de marcher dans 20 ans, nous pouvons utiliser un nombre 64-bit à la place - mais nos utilisateurs auront besoin de dépenser plus de gas pour utiliser notre DApp pendant ce temps. Décision de conception !

## Random

>La meilleure source d'aléatoire que nous avons avec Solidity est la fonction de hachage keccak256. Bien sur, puisque des dizaine de milliers de nœuds Ethereum sur le réseau rivalisent pour résoudre le prochain bloc, mes chances de résoudre le prochain bloc sont vraiment faibles. Il me faudrait énormément de puissance de calcul et de temps pour réussir à l'exploiter - mais si la récompense est assez élevée (si je pouvais parier 100 000 000$ sur la fonction pile ou face), cela vaudrait la peine de l'attaquer.
Même si cette fonction aléatoire N'EST PAS sécurisée sur Ethereum, en pratique, à part si notre fonction aléatoire a beaucoup d'argent en jeu, les utilisateurs de votre jeu n'auront sûrement pas assez de ressources pour l'attaquer.

Exemple d'aléatoire non parfaitement sécurisé :<br>
`uint randNonce = 0;`<br>
`uint random = uint(keccak256(now, msg.sender, randNonce)) % 100;`

Remarque : Une des possibilités serait d'avoir un oracle pour avoir accès à une fonction aléatoire en dehors de la blockchain Ethereum.

## Payable

>Une des choses qui rend Solidity et Ethereum vraiment cool est le modificateur payable, une fonction payable est une fonction spéciale qui peut recevoir des Ether.
Réfléchissons une minute. Quand vous faites un appel à une fonction API sur un serveur normal, vous ne pouvez pas envoyer des dollars US en même temps - pas plus que des Bitcoin.
Mais en Ethereum, puisque la monnaie (Ether), les données (charge utile de la transaction) et le code du contrat lui-même sont directement sur Ethereum, il est possible pour vous d'appeler une fonction et de payer le contrat en même temps.
Cela permet un fonctionnement vraiment intéressant, comme demander un certain paiement au contrat pour pouvoir exécuter une fonction.

Remarque : on peut utiliser un require avec comme condition "msg.value == 0.1 ether" dans la fonction modifier par payable :<br>
`require(msg.value == 0.001 ether);`<br>
Puis depuis web3.js par exemple on appelle la fonction buySomething() modifier par payable avec :<br>
`OnlineStore.buySomething({from: web3.eth.defaultAccount, value: web3.utils.toWei(0.001)})`

## Retraits

>Après avoir envoyé des Ether à un contrat, ils sont stockés dans le compte Ethereum du contrat, et resteront ici - à part si vous ajoutez une fonction pour retirer les Ether du contrat. Vous pouvez transférer des Ether à une adresse en utilisant la fonction transfer, et this.balance retournera la balance totale stockée sur le contrat. Si 100 utilisateurs ont payé 1 Ether à votre contrat, this.balance sera égal à 100 Ether. Vous pouvez utilisez transfer pour envoyer des fonds à n'importe quelle adresse Ethereum.

Remarque : on peut ainsi rendre la monnaie sur un paiement :<br>
`uint itemFee = 0.001 ether;`<br>
`msg.sender.transfer(msg.value - itemFee);`

## Ownable

>Dans le cas de onlyOwner, rajouter ce modificateur à une fonction fera 
en sorte que seulement le propriétaire du contrat (vous, si vous l'avez déployé) pourra appeler cette fonction.
"onlyOwner" est une condition si courante pour les contrats que la plupart 
des DApps Solidity commencent par copier/coller ce contrat Ownable, et leur premier contrat en hérite.
>Pour résumer le contrat Ownable fait fondamentalement ceci :
> - Quand un contrat est créé, son constructeur défini le owner égal à msg.sender (la personne qui le déploie)
> - Il ajoute un modificateur onlyOwner, qui permet de restreindre l'accès à certaines fonctions à seulement le owner
> - Il vous permet de transférer le contrat à un nouvel owner

Remarque : Donner des privilèges spéciaux au propriétaire du contrat comme là est souvent nécessaire, cependant cela pourrait aussi être utilisé malicieusement. Par exemple, le propriétaire pourrait ajouter une fonction de porte dérobée qui lui permettrait de transférer n'importe quel zombie à lui-même !
C'est donc important de se rappeler que ce n'est pas parce qu'une DApp est sur Ethereum que cela veut dire qu'elle est décentralisée - vous devez lire le code source en entier pour vous assurez que le propriétaire n'a pas de privilèges qui pourraient vous inquiéter. En tant que développeur, il existe un équilibre entre garder le contrôle d'un DApp pour corriger de potentiels bugs, et construire une plateforme sans propriétaire en laquelle vos utilisateurs peuvent avoir confiance pour sécuriser leurs données.

## SafeMath

>Une bibliothèque est un type de contrat spécial en Solidity. Une de leurs fonctionnalités est que cela permet de rajouter des fonctions à un type de donnée native.
Par exemple. avec la bibliothèque SafeMath, nous allons utiliser la syntaxe using SafeMath for uint256. La bibliothèque SafeMath à 4 fonctions — add, sub, mul, et div. Et maintenant nous pouvons utiliser ces fonctions à partir d'un uint256 en faisant.
add ajoute simplement 2 uint comme +, mais elle contient aussi une déclaration assert (affirme) pour vérifier que la somme est plus grande que a. Cela nous protège d'un débordement.
assert est la même chose que require, et va renvoyer une erreur si ce n'est pas vérifié. La différence entre assert et require c'est que require va rembourser l'utilisateur du gas restant quand la fonction échoue, alors que assert non. La plupart du temps vous allez vouloir utiliser require dans votre code, assert est plutôt utilisé quand quelque chose a vraiment mal tourné avec le code (comme un débordement d'uint).
Pour résumer, add, sub, mul, et div de SafeMath sont des fonctions qui font les 4 opérations mathématiques basiques, et qui renvoient une erreur en cas de débordement

Exemple : utilisation de SafeMath<br>
`using SafeMath for uint256;`<br>
`uint256 a = 5;`<br>
`uint256 b = a.add(3);`<br>
`uint256 c = a.mul(2);`<br>

## Tokens ethereum

>Si vous êtes dans la sphère Ethereum depuis un moment, vous avez sûrement entendu parler des tokens - en particulier des tokens ERC20.
un token Ethereum est un smart contract qui suit un ensemble de règles - à savoir, il implémente un ensemble de fonctions standards que tous les autres contrats de token partagent, comme transfer(address _to, uint256 _value) et balanceOf(address _owner).
Le smart contract a habituellement un mappage interne, mapping(address => uint256) balances, qui permet de connaître la balance de chaque adresse.
Un token est simplement un contrat qui permet de connaître combien de ce token chaque personne possède, et qui a certaines fonctions pour permettre aux utilisateurs de transférer leurs tokens à d'autres adresses.

Exemple : Code du token ERC721 a importer et implementer<br>
`contract ERC721 {`<br>
` event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);`<br>
`event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId);`<br>
`function balanceOf(address _owner) public view returns (uint256 _balance);`<br>
`function ownerOf(uint256 _tokenId) public view returns (address _owner);`<br>
`function transfer(address _to, uint256 _tokenId) public;`<br>
`function approve(address _to, uint256 _tokenId) public;`<br>
`function takeOwnership(uint256 _tokenId) public;`<br>
`}` 

------------------------------------------------------------------

## ----------------FRONTEND----------------

------------------------------------------------------------------

### Provider

>La première chose dont nous avons besoin, c'est d'un fournisseur (provider) Web3.
Rappelez-vous, Ethereum est fait de nœuds qui partagent une copie des mêmes données. Configurer un fournisseur Web3 indique à notre code avec quel nœud nous devrions communiquer pour traiter nos lectures et écritures. C'est un peu comme configurer l'URL d'un serveur web distant pour des appels API d'une application web classique.
Vous pourriez héberger votre propre nœud Ethereum comme fournisseur. Mais il existe un service tiers qui vous facilitera la vie pour que vous n'ayez pas besoin de vous occuper de votre propre nœud Ethereum pour fournir une DApp à vos utilisateurs - Infura.

### Infura 

>Infura est un service qui a plusieurs nœuds Ethereum avec une fonctionnalité de cache pour des lectures plus rapides, que vous pouvez accéder gratuitement depuis leur API. En utilisant Infura comme fournisseur, vous pouvez envoyer et recevoir des messages de la blockchain Ethereum de manière fiable, sans avoir à vous occuper de votre propre nœud.

## Private key

>Ethereum (et les blockchains en général) utilise une paire de clés publique / privée pour signer numériquement les transactions. C'est un peu comme un mot de passe extrêmement compliqué pour signer numériquement. Ainsi, si je change des données sur la blockchain, je peux prouver grâce à la clé publique que je suis celui qui les a signées - mais puisque personne ne connaît ma clé privée, personne ne peut créer une transaction à ma place.

## MetaMask

>MetaMask est une extension Chrome et Firefox qui permet aux utilisateurs de gérer de manière sécurisée leurs comptes Ethereum et leurs clés privées, et d'utiliser ces comptes pour interagir avec les sites web qui utilisent Web3.js. (Si vous ne l'avez jamais utilisé, vous devriez vraiment l'installer - ainsi votre navigateur web sera compatible avec Web3, et vous allez pouvoir interagir avec tous les sites qui communiquent avec la blockchain Ethereum !).

Remarque : MetaMask utilise les serveurs d'Infura comme fournisseur web3, comme nous avons fait ci-dessus - mais il offre aussi la possibilité à l'utilisateur d'utiliser son propre fournisseur web3. En utilisant le fournisseur web3 de MetaMask, vous donnez à votre utilisateur le choix, et c'est une chose de moins à gérer pour votre application.

Template : [startApp()](https://github.com/hanzopgp/CryptoZombies/blob/main/src/public/index.html)

## ABI

>ABI veut dire "Application Binary Interface" (Interface Binaire d'Application). Fondamentalement, c'est une représentation des fonctions de votre contrat au format JSON qui indique à Web3.js comment formater les appels aux fonctions pour que votre contrat les comprenne.
Quand vous compilez votre contrat pour le déployer sur Ethereum (ce que nous verrons dans la Leçon 7), le compilateur Solidity vous donnera son ABI, vous aller devoir le copier et le sauvegarder en plus de l'adresse de votre contrat.

## Appel des fonctions du contract

### --> Call

>call est utilisé pour les fonctions view etpure. C'est exécuté seulement sur le nœud local, et cela ne va pas créer de transaction sur la blockchain.

Remarque : Les fonctions view et pure sont des fonctions en lecture seule et ne changent pas l'état de la blockchain. Elles ne coûtent pas de gas et l'utilisateur n'aura pas besoin de signer de transaction avec MetaMask.

### --> Send

>send va créer une transaction et changer l'état des données sur la blockchain. Vous aurez besoin d'utiliser send pour toutes les fonctions qui ne sont pas view ou pure.

Remarque : Envoyer une transaction avec send demandera à l'utilisateur de payer du gas, en faisant apparaître MetaMask pour leur demander de signer une transaction. Quand on utilise MetaMask comme fournisseur web3, tout cela se fait automatiquement quand on appelle send(), et on n'a pas besoin de faire quoique ce soit de spécial dans notre code.

## Getter automatique

>En Solidity, quand vous déclarez une variable public, cela crée automatiquement une fonction "getter" (une fonction de récupération) public avec le même nom. Si vous voulez récupérer le zombie avec l'id 15, vous l’appellerez comme si c'était une fonction : zombies(15).

## Recuperer l'adresse de l'user et la mettre a jour

>Puisque l’utilisateur peut changer de compte actif n'importe quand avec MetaMask, notre application a besoin de surveiller cette variable pour voir si elle change et mettre à jour l'interface en conséquence. Par exemple, si la page d'accueil montre l'armée de zombie d'un utilisateur, quand il change de compte dans MetaMask, nous allons vouloir mettre à jour la page pour montrer l'armée de zombie du nouveau compte sélectionné.

Template : [setInterval()](https://github.com/hanzopgp/CryptoZombies/blob/main/src/public/index.html)

## Envoyer des transactions

>Maintenant nous allons voir comment changer les données de notre smart contract avec les fonctions send.

Remarque : Il y a un certain délais entre le moment où l'utilisateur envoie une transaction avec send et le moment où cette transaction prend effet sur la blockchain. C'est parce qu'il faut attendre que la transaction soit incluse dans un bloc, et un bloc est créé toutes les 15 sec environ avec Ethereum. S'il y a beaucoup de transactions en attente, ou si l'utilisateur paye un prix de gas trop bas, notre transaction pourrait attendre plusieurs blocs avant d'être incluse, et cela pourrait prendre plusieurs minutes.

>Notre fonction envoie avec send une transaction à notre fournisseur Web3, et met à la chaîne un écouteur d'évènements :
receipt (reçu) va être émis quand la transaction est incluse dans un bloc Ethereum, ce qui veut dire que notre zombie a été créé et sauvegardé dans notre contrat.
error (erreur) va être émis s'il y a un problème qui empêche la transaction d'être incluse dans un bloc, tel qu'un envoie insuffisant de gas par l'utilisateur. Nous allons vouloir informer l'utilisateur que la transaction n'a pas marché pour qu'il puisse réessayer.

Remarque : Vous avec le choix de spécifier le gas et gasPrice quand vous appelez send, ex : .send({ from: userAccount, gas: 3000000 }). Si vous ne le spécifiez pas, MetaMask va laisser l'utilisateur choisir ces valeurs.

## Payable

>Il est facile d'indiquer combien d'Ether envoyer avec une fonction, en faisant attention à une chose : nous devons spécifier combien envoyer en wei, pas en Ether. Un wei est la plus petite sous-unité d'un Ether - il y a 10^18 wei dans un ether.

Exemple : Envoie de 0.001 ether pour levelUp un zombie<br>
`cryptoZombies.methods.levelUp(zombieId).send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })`<br>
