# Resolução do CTF do Estágio da Tempest Security

## Contexto

A empresa Quantum Nexys foi alvo de um ataque de ransomware que resultou na criptografia de uma patente para a qual ainda não havia sido realizado o backup. Os cibercriminosos deixaram a patente criptografada usando PGP e enviaram um link para que o pagamento fosse efetuado, permitindo assim a obtenção da chave privada necessária para descriptografar os arquivos. A partir dessas informações, o objetivo do CTF era:

1. Descobrir o nome do grupo responsável pelo ataque.
2. Identificar qual membro do grupo realizou o ataque.
3. Conseguir o ID da patente após descriptografar a mensagem PGP.

## 1) Descobrindo o Nome do Grupo

Ao acessar o link deixado pelos cibercriminosos, encontramos um formulário que deveria ser preenchido com o ID da empresa e o ID da carteira para o pagamento.

<img src="https://i.imgur.com/l00h77y.png" height="80%" width="80%" alt="Payment Form"/>

Analisando o código-fonte, observamos uma função responsável por enviar os dados do formulário para um site diferente do qual estava hospedado o formulário.

<img src="https://i.imgur.com/KeHSpt6.png" height="80%" width="80%" alt="Payment Form"/>

Ao acessar o link encontrado, deparamos com uma página em branco. No entanto, ao reduzir o link para `https://forum.tsi-lab.com.br/`, fomos redirecionados para uma tela de login que exibia o nome do grupo, conseguindo assim a primeira flag.

<img src="https://i.imgur.com/Lhxx3Op.png" height="80%" width="80%" alt="Payment Form"/>

## 2) Identificando o Membro que Realizou o Ataque

Analisando o arquivo `https://forum.tsi-lab.com.br/robots.txt`, encontramos os caminhos não listados, e, ao explorar cada um, descobrimos o caminho `/api/swagger.yaml`. Esse arquivo nos forneceu uma visão melhor da estrutura e dos endpoints da API alvo, além de incluir credenciais de login de um membro.

<img src="https://i.imgur.com/PM1NpCi.png" height="80%" width="80%" alt="Payment Form"/>

Usamos essas credenciais para realizar o login no fórum, mas a conta estava desativada e não nos fornecia acesso aos fóruns, mensagens e campanhas. Apenas era possível acessar o perfil e as regras e condutas do grupo.

<img src="https://i.imgur.com/V54tEPY.png" height="80%" width="80%" alt="Payment Form"/>

No entanto, ao verificar as informações sobre a aplicação, percebemos que um cookie foi gerado no momento do login.

<img src="https://i.imgur.com/8NcC4zn.png" height="80%" width="80%" alt="Payment Form"/>

Decodificando o cookie usando o [JWT.io](https://jwt.io), descobrimos que as informações nele contidas eram referentes ao usuário, incluindo o status "enabled", que estava como "false". Comparando com as informações no fórum, vimos que os dados correspondiam ao perfil.

<img src="https://i.imgur.com/ZboyWrv.png" height="80%" width="80%" alt="Payment Form"/>

Como não havia um salt para aumentar a segurança da hash, alteramos o valor do parâmetro "enabled" para "true". Substituímos o cookie que estava sendo usado por este novo e, assim, conseguimos acesso às outras páginas que antes não tínhamos autorização para acessar. Com isso, conseguimos a nossa segunda flag.

<img src="https://i.imgur.com/NOXqG98.png" height="80%" width="80%" alt="Payment Form"/>

## 3) Obtendo a Chave Privada e a Última Flag

Com o nome do responsável pelo ataque em mãos, mais uma vez alteramos os parâmetros do cookie para obter acesso à conta dele.

<img src="https://i.imgur.com/lw7Sp3a.png" height="80%" width="80%" alt="Payment Form"/>

Ao acessar o perfil do atacante, vimos que havia apenas a chave pública disponível, mas era necessário obter a chave privada.

<img src="https://i.imgur.com/HsHJWY8.png" height="80%" width="80%" alt="Payment Form"/>

Explorando os endpoints, acessamos os arquivos de chaves da campanha em questão e percebemos a existência de dois arquivos muito semelhantes.

<img src="https://i.imgur.com/M7lmq8B.png" height="80%" width="80%" alt="Payment Form"/>

Baseado nessas informações, percebemos que o arquivo chamado no perfil era `id_pgp.pub`. Alterando o parâmetro para `id_pgp`, conseguimos obter a chave privada.

<img src="https://i.imgur.com/MCORo0V.png" height="80%" width="80%" alt="Payment Form"/>

Com a mensagem fornecida no início e a chave privada em mãos, utilizamos o [CyberChef](https://gchq.github.io/CyberChef/) para descriptografar a mensagem, conseguindo assim a última flag.

<img src="https://i.imgur.com/Z3IZMKs.png" height="80%" width="80%" alt="Payment Form"/>

---

Este documento detalha a resolução do CTF realizado durante o programa de estágio da Tempest Security, demonstrando as etapas de descoberta e exploração para alcançar os objetivos propostos.
