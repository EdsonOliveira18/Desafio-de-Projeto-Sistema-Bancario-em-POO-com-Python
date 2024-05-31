# Sistema Bancário em POO com Python

Este projeto implementa um sistema bancário simples em Python utilizando Programação Orientada a Objetos (POO). O sistema permite a criação de usuários, abertura de contas, realização de depósitos, saques, exibição de extratos e listagem de contas.

## Índice

1. [Visão Geral](#visão-geral)
2. [Instalação](#instalação)
3. [Uso](#uso)
4. [Funções](#funções)
    - [menu](#menu)
    - [depositar](#depositar)
    - [sacar](#sacar)
    - [exibir_extrato](#exibir_extrato)
    - [criar_usuario](#criar_usuario)
    - [filtrar_usuario](#filtrar_usuario)
    - [criar_conta](#criar_conta)
    - [listar_contas](#listar_contas)
5. [Função Principal](#função-principal)
6. [Conclusão](#conclusão)

## Visão Geral

O sistema bancário em Python permite a gestão básica de contas e usuários, com funcionalidades para depósitos, saques, exibição de extratos e listagem de contas. O sistema é controlado por um menu interativo que guia o usuário através das diferentes operações disponíveis.

## Instalação

1. Clone o repositório:
    ```sh
    git clone https://github.com/seu-usuario/nome-do-repositorio.git
    ```
2. Navegue até o diretório do projeto:
    ```sh
    cd nome-do-repositorio
    ```

## Uso

Execute o script principal para iniciar o sistema bancário:
```sh
python nome_do_arquivo.py
```

## Funções

### menu

Exibe o menu de opções para o usuário e retorna a opção escolhida.

```python
def menu():
    menu = """\n
    ================ MENU ================
    [d]\tDepositar
    [s]\tSacar
    [e]\tExtrato
    [nc]\tNova conta
    [lc]\tListar contas
    [nu]\tNovo usuário
    [q]\tSair
    => """
    return input(textwrap.dedent(menu))
```

### depositar

Realiza um depósito na conta. Se o valor for positivo, ele é adicionado ao saldo e registrado no extrato.

``` python
def depositar(saldo, valor, extrato, /):
    if valor > 0:
        saldo += valor
        extrato += f"Depósito:\tR$ {valor:.2f}\n"
        print("\n=== Depósito realizado com sucesso! ===")
    else:
        print("\n@@@ Operação falhou! O valor informado é inválido. @@@")

    return saldo, extrato
```

### sacar

Permite ao usuário realizar um saque da conta, verificando se há saldo suficiente, se o valor do saque está dentro do limite e se o número de saques não excedeu o máximo permitido.

```python
def sacar(*, saldo, valor, extrato, limite, numero_saques, limite_saques):
    excedeu_saldo = valor > saldo
    excedeu_limite = valor > limite
    excedeu_saques = numero_saques >= limite_saques

    if excedeu_saldo:
        print("\n@@@ Operação falhou! Você não tem saldo suficiente. @@@")

    elif excedeu_limite:
        print("\n@@@ Operação falhou! O valor do saque excede o limite. @@@")

    elif excedeu_saques:
        print("\n@@@ Operação falhou! Número máximo de saques excedido. @@@")

    elif valor > 0:
        saldo -= valor
        extrato += f"Saque:\t\tR$ {valor:.2f}\n"
        numero_saques += 1
        print("\n=== Saque realizado com sucesso! ===")

    else:
        print("\n@@@ Operação falhou! O valor informado é inválido. @@@")

    return saldo, extrato
```

### exibir_extrato

Exibe o extrato da conta, mostrando todas as movimentações e o saldo atual.

```python
def exibir_extrato(saldo, /, *, extrato):
    print("\n================ EXTRATO ================")
    print("Não foram realizadas movimentações." if not extrato else extrato)
    print(f"\nSaldo:\t\tR$ {saldo:.2f}")
    print("==========================================")
```

### criar_usuario

Cria um novo usuário no sistema, coletando informações como CPF, nome, data de nascimento e endereço.

```python
def criar_usuario(usuarios):
    cpf = input("Informe o CPF (somente número): ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\n@@@ Já existe usuário com esse CPF! @@@")
        return

    nome = input("Informe o nome completo: ")
    data_nascimento = input("Informe a data de nascimento (dd-mm-aaaa): ")
    endereco = input("Informe o endereço (logradouro, nro - bairro - cidade/sigla estado): ")

    usuarios.append({"nome": nome, "data_nascimento": data_nascimento, "cpf": cpf, "endereco": endereco})

    print("=== Usuário criado com sucesso! ===")
```

### filtrar_usuario

Busca um usuário na lista de usuários cadastrados com base no CPF fornecido.

```python
def filtrar_usuario(cpf, usuarios):
    usuarios_filtrados = [usuario para usuario em usuarios se usuario["cpf"] == cpf]
    return usuarios_filtrados[0] se usuarios_filtrados mais None
```

### criar_conta

Cria uma nova conta bancária associada a um usuário existente. Se o CPF fornecido não corresponder a um usuário cadastrado, a criação da conta é cancelada.

```python
def criar_conta(agencia, numero_conta, usuarios):
    cpf = input("Informe o CPF do usuário: ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\n=== Conta criada com sucesso! ===")
        return {"agencia": agencia, "numero_conta": numero_conta, "usuario": usuario}

    print("\n@@@ Usuário não encontrado, fluxo de criação de conta encerrado! @@@")
```

### listar_contas

Exibe todas as contas cadastradas no sistema, incluindo agência, número da conta e nome do titular.

```python
def listar_contas(contas):
    para conta em contas:
        linha = f"""\
            Agência:\t{conta['agencia']}
            C/C:\t\t{conta['numero_conta']}
            Titular:\t{conta['usuario']['nome']}
        """
        print("=" * 100)
        print(textwrap.dedent(linha))
```

## Função Principal

A função `main` controla o fluxo principal do programa, inicializando variáveis e executando um loop que exibe o menu e chama as funções apropriadas com base na entrada do usuário.

```python
def main():
    LIMITE_SAQUES = 3
    AGENCIA = "0001"

    saldo = 0
    limite = 500
    extrato = ""
    numero_saques = 0
    usuarios = []
    contas = []

    while True:
        opcao = menu()

        if opcao == "d":
            valor = float(input("Informe o valor do depósito: "))

            saldo, extrato = depositar(saldo, valor, extrato)

        elif opcao == "s":
            valor = float(input("Informe o valor do saque: "))

            saldo, extrato = sacar(
                saldo=saldo,
                valor=valor,
                extrato=extrato,
                limite=limite,
                numero_saques=numero_saques,
                limite_saques=LIMITE_SAQUES,
            )

        elif opcao == "e":
            exibir_extrato(saldo, extrato=extrato)

        elif opcao == "nu":
            criar_usuario(usuarios)

        elif opcao == "nc":
            numero_conta = len(contas) + 1
            conta = criar_conta(AGENCIA, numero_conta, usuarios)

            if conta:
                contas.append(conta)

        elif opcao == "lc":
            listar_contas(contas)

        elif opcao == "q":
            break

        else:
            print("Operação inválida, por favor selecione novamente a operação desejada.")
```

## Conclusão

Este sistema bancário em Python implementa as funcionalidades básicas de um banco, permitindo a criação e gerenciamento de contas e usuários, bem como a realização de transações bancárias simples. O uso de funções para modularizar o código facilita a manutenção e a expansão do sistema.
