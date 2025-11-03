# O que é um Hash?

Um **hash** é o resultado de uma função matemática que transforma uma quantidade arbitrária de dados (como um arquivo, uma senha ou uma mensagem) em uma saída de tamanho fixo. Essa saída, também chamada de *digest*, é uma sequência única de caracteres que representa os dados originais.

A principal utilidade de um hash é garantir a **integridade dos dados**. Ele funciona como um "lacre digital" ou uma "impressão digital" para os seus dados.

## Propriedades de um Bom Hash

Uma boa função de hash criptográfica (Cryptographic Hash Function - CHF) tem algumas características essenciais:

1.  **Tamanho Fixo:** A saída (hash) tem sempre o mesmo tamanho, não importa o tamanho da entrada (seja um arquivo de 1KB ou 10GB).
2.  **Determinística:** A mesma entrada *sempre* gerará a mesma saída.
3.  **Efeito Avalanche:** Uma mudança *mínima* na entrada (mudar um bit, adicionar um espaço) gera uma saída (hash) *completamente* diferente.
4.  **Mão Única (One-Way):** É computacionalmente inviável reverter o processo, ou seja, descobrir a entrada original a partir do seu hash.
5.  **Resistência à Colisão:** É (ou deveria ser) extremamente difícil encontrar duas entradas diferentes que gerem o mesmo hash.

## A Analogia do Liquidificador Mágico

Pense em uma função de hash como um liquidificador mágico e muito potente:

1.  Você coloca seus ingredientes (os dados de entrada, como um arquivo de texto com "frutas, leite e açúcar").
2.  O liquidificador processa tudo e entrega uma "batida" (o hash).

O importante aqui é:

* **É de mão única:** Olhando apenas para a batida pronta (o hash), é impossível saber exatamente quais ingredientes você usou ou re-separá-los para voltar ao original.
* **É sensível:** Se você adicionar *uma única gota* a mais de um ingrediente (mudar um caractere no arquivo), a batida final sairá com uma cor e sabor *completamente* diferentes (o hash muda drasticamente).
* **É previsível:** Se você usar *exatamente* os mesmos ingredientes na mesma ordem, a batida final será *sempre* idêntica.

## Na Prática: Verificando a Integridade de Arquivos

Vamos ver isso funcionando. Para este exemplo, vou usar o algoritmo `SHA1` (embora hoje em dia `SHA-256` ou superior seja mais recomendado por segurança).

---

### O Cenário

Primeiro, criei dois arquivos de texto: `original_batida.txt` e `nova_batida.txt`.

O arquivo `original_batida.txt` tem a seguinte lista de ingredientes:

<img width="436" height="326" alt="image" src="https://github.com/user-attachments/assets/e52d398d-f25c-47f7-b53e-69359dcebfc8" />


O arquivo `nova_batida.txt` tem *exatamente* o mesmo conteúdo.

<img width="410" height="335" alt="image" src="https://github.com/user-attachments/assets/2f2569f0-4e4d-4a09-b85b-b4bbff77ddec" />


### Teste 1: Arquivos Idênticos

No PowerShell, já na pasta onde os arquivos estão, vamos gerar o hash `SHA1` do primeiro arquivo com o comando:
`Get-Filehash .\original_batida.txt -Algorithm SHA1`

<img width="1308" height="129" alt="image" src="https://github.com/user-attachments/assets/679f0190-672b-4502-b0de-25714df2e4bc" />


O hash gerado foi: `A55C0EF75540EC19A4D3AF4363CCF2E6F240C4BE`. Esta é a "impressão digital" do nosso arquivo.

Agora, fazendo o mesmo com o arquivo `nova_batida.txt`:

<img width="1264" height="124" alt="image" src="https://github.com/user-attachments/assets/c96b534d-a58c-4f95-a721-baab7f3afea8" />


Note que o hash é **idêntico**. Isso prova que os dois arquivos são 100% iguais. É assim que verificamos a integridade de downloads: o site fornece o hash esperado, e você o compara com o hash do arquivo que baixou. Se baterem, o arquivo não foi corrompido ou alterado.

### Teste 2: A Mínima Alteração

E se eu retirar apenas uma banana da lista no `nova_batida.txt` e tentar gerar o hash de novo?

O arquivo ficou assim:

<img width="418" height="312" alt="image" src="https://github.com/user-attachments/assets/023bd080-2a37-4481-b899-08b201eb5f01" />


E o novo hash é:

<img width="1255" height="127" alt="image" src="https://github.com/user-attachments/assets/6c3af83f-c249-45cf-8db6-7a9d36b7cdfd" />


Veja! O hash mudou **completamente**. Qualquer alteração, por menor que seja (até um espaço em branco), resulta em um hash totalmente diferente. Isso é o **Efeito Avalanche** em ação.

## E as Senhas?

Como o hash é uma "mão única", ele é perfeito para armazenar senhas. Um sistema seguro **nunca** guarda a sua senha "123456". Ele guarda o *hash* dessa senha.

Quando você tenta logar, o sistema:
1.  Pega a senha que você digitou ("123456").
2.  Calcula o hash dela usando o mesmo algoritmo.
3.  Compara o *hash calculado* com o *hash guardado* no banco de dados.

Se os hashes baterem, você entra. O sistema nunca precisou saber sua senha original. A única forma de "quebrar" isso seria por tentativa e erro (força bruta), testando milhões de senhas por segundo, calculando o hash de cada uma e comparando.

## Limitações: Colisões e Algoritmos

Nenhum algoritmo é 100% perfeito. Como a saída tem tamanho fixo, mas a entrada pode ser infinita, é matematicamente possível que duas entradas diferentes gerem o mesmo hash. Isso é chamado de **colisão**.

Algoritmos mais antigos, como `MD5` e `SHA1`, já se mostraram vulneráveis a colisões (pessoas descobriram como criar dois arquivos diferentes com o mesmo hash de propósito). Por isso, eles não são mais considerados seguros para muitas aplicações.

É por isso que existem tantas variações e por que migramos para algoritmos mais fortes (`SHA-256`, `SHA-3`), que tornam a chance de uma colisão acidental ou maliciosa astronomicamente pequena.

### Principais Famílias de Algoritmos

* **Message Digest (MD):** Focado na verificação de integridade. As versões mais comuns são MD2, MD4 e MD5 (hoje considerado obsoleto por questões de segurança).
* **Secure Hash Function (SHA):** Padrão da indústria, usado em certificados SSL/TLS, assinaturas digitais e mais. Inclui `SHA-1` (obsoleto), `SHA-256` e `SHA-3`.
* **RIPEMD:** Uma família de funções de hash desenvolvida na Europa, com saídas de 160 bits (similar ao `SHA-1`).
* **Whirlpool:** Um algoritmo de código livre desenvolvido, entre outros, por um brasileiro. É adotado por padrões internacionais (ISO/IEC).

## Exemplo prático:
Criei esse script que serve como um exemplo prático.
Ele transforma um arquivo em hash e verifica se esse arquivo foi conectado
- repo: https://github.com/theycallmefreire/File-Integrity-Monitor-Simple


## Fontes:
- https://www.youtube.com/watch?v=4_s9lOuUpZ4&list=PLqBeiU46hx1H--SNfTrohTOWeqkK-M2Y0
- https://voitto.com.br/blog/artigo/o-que-e-hash-e-como-funciona
