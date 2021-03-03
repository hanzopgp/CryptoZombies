# CryptoZombies

## Presentation

>This is the cryptozombies dapp tutoriel source code from [cryptozombies](https://www.cryptozombies.io).

## Memento

- Demander confirmation avant d'executer le code qui suit
`require(condition)`
- Recuperer l'id de la personne executant le smart contract
`msg.sender`
- Exemple de mapping 
`mapping (uint => address) public zombieToOwner;
mapping (address => uint) ownerZombieCount;`
- Exemple utilisation du mapping
`zombieToOwner[id] = msg.sender;
ownerZombieCount[msg.sender]++;`
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
- Initialisation d'une interface KittyInterface grace a l'adresse du smart contract
`address ckAddress = 0x06012c8cf97BEaD5deAe237070F9587f8E7A266d;
KittyInterface kittyContract = KittyInterface(ckAddress);`

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
