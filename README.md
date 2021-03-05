# CryptoZombies

## Presentation

>This is the cryptozombies dapp tutoriel source code from [cryptozombies](https://www.cryptozombies.io).

## Memento

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
}`

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

## Payable

>Une des choses qui rend Solidity et Ethereum vraiment cool est le modificateur payable, une fonction payable est une fonction spéciale qui peut recevoir des Ether.
Réfléchissons une minute. Quand vous faites un appel à une fonction API sur un serveur normal, vous ne pouvez pas envoyer des dollars US en même temps - pas plus que des Bitcoin.
Mais en Ethereum, puisque la monnaie (Ether), les données (charge utile de la transaction) et le code du contrat lui-même sont directement sur Ethereum, il est possible pour vous d'appeler une fonction et de payer le contrat en même temps.
Cela permet un fonctionnement vraiment intéressant, comme demander un certain paiement au contrat pour pouvoir exécuter une fonction.

Remarque : on peut utiliser un require avec comme condition "msg.value == 0.1 ether" dans la fonction modifier par payable :<br>
`require(msg.value == 0.001 ether);`<br>
Puis depuis web3.js par exemple on appelle la fonction buySomething() modifier par payable avec :<br>
`OnlineStore.buySomething({from: web3.eth.defaultAccount, value: web3.utils.toWei(0.001)})`

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
