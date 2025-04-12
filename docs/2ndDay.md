# Configuração do Ngrok no Google Colab - Dia 2 do #7DaysOfCode

Este documento detalha os passos para configurar o Ngrok no Google Colab como parte do desafio #7DaysOfCode de desenvolvimento de chatbot em Python.

## Visão Geral
O Ngrok é uma ferramenta essencial que cria túneis seguros para expor serviços locais na internet através de URLs públicas, permitindo que aplicações em desenvolvimento sejam acessíveis externamente para testes e demonstrações.

## Passo a Passo Detalhado

### 1. Criar um novo notebook no Google Colab
- Acessar [colab.research.google.com](https://colab.research.google.com/)
- Criar um novo notebook chamado "Dia2-Configuracao-Ngrok"
- Salvar no repositório GitHub na pasta `/notebooks`

### 2. Verificar a instalação do PyNgrok
```python
# Verificar se o pyngrok já está instalado
try:
    from pyngrok import ngrok
    print("PyNgrok já está instalado!")
except ImportError:
    print("PyNgrok não está instalado. Instalando agora...")
    !pip install pyngrok -q
    print("PyNgrok instalado com sucesso!")
```

### 3. Criar uma conta no Ngrok (se necessário)
```python
# Execute esta célula e clique no link para criar uma conta no Ngrok
from IPython.display import display, HTML
display(HTML('<a href="https://dashboard.ngrok.com/signup" target="_blank">Clique aqui para criar uma conta no Ngrok</a>'))
```

### 4. Configurar o token de autenticação
```python
# Obtenha seu authtoken em: https://dashboard.ngrok.com/auth/your-authtoken
import os
from IPython.display import display, HTML

display(HTML('<a href="https://dashboard.ngrok.com/auth/your-authtoken" target="_blank">Clique aqui para obter seu Ngrok authtoken</a>'))

# Função para configurar o token do Ngrok
def configurar_ngrok_token():
    token = input("Cole aqui seu token do Ngrok: ")
    !ngrok authtoken {token}
    # Também salvar o token para uso no código Python
    os.environ["NGROK_AUTH_TOKEN"] = token
    print("Token do Ngrok configurado com sucesso!")

configurar_ngrok_token()
```

### 5. Testar o funcionamento do Ngrok
```python
# Teste de funcionamento do Ngrok
from pyngrok import ngrok
import time

# Listar túneis ativos (deve estar vazio inicialmente)
print("Túneis ativos antes de criar um novo:")
print(ngrok.get_tunnels())

# Criar um túnel de teste (na porta 8000)
print("\nCriando um túnel de teste...")
http_tunnel = ngrok.connect(8000)
print(f"URL pública do Ngrok: {http_tunnel.public_url}")

# Listar túneis ativos novamente
print("\nTúneis ativos depois de criar um novo:")
print(ngrok.get_tunnels())

# Esperar 5 segundos para que você possa ver o resultado
time.sleep(5)

# Encerrar o túnel
print("\nEncerrando o túnel...")
ngrok.disconnect(http_tunnel.public_url)
ngrok.kill()
print("Túnel encerrado!")
```

### 6. Funções reutilizáveis para gerenciamento de túneis Ngrok
```python
# Função para criar um túnel Ngrok
def criar_tunel_ngrok(porta, auth_token=None):
    """
    Cria um túnel Ngrok para a porta especificada.
    
    Args:
        porta (int): Porta local que você deseja expor.
        auth_token (str, opcional): Token de autenticação do Ngrok. 
                                   Se não fornecido, usa o token já configurado.
    
    Returns:
        str: URL pública do túnel Ngrok.
    """
    from pyngrok import ngrok
    import os
    
    # Verificar se o token está definido
    if auth_token:
        os.environ["NGROK_AUTH_TOKEN"] = auth_token
        !ngrok authtoken {auth_token}
    
    # Criar o túnel
    public_url = ngrok.connect(porta)
    print(f"Túnel Ngrok criado com sucesso!")
    print(f"URL pública: {public_url}")
    
    return public_url

# Função para encerrar um túnel Ngrok
def encerrar_tunel_ngrok(url_public):
    """
    Encerra um túnel Ngrok específico.
    
    Args:
        url_public (str): URL pública do túnel a ser encerrado.
    """
    # Encerrar o túnel
    print("\nEncerrando o túnel...")
    ngrok.disconnect(url_public)
    ngrok.kill()
    print("Túnel encerrado!")

# Exemplo de como usar estas funções (comentado para não executar automaticamente)
# url = criar_tunel_ngrok(porta=8000)
# encerrar_tunel_ngrok(url)
```

## Observações Importantes
1. **Persistência do token**: A autenticação será necessária a cada nova sessão do Colab, já que o ambiente não mantém configurações entre sessões.
2. **Plano gratuito**: O plano gratuito do Ngrok tem algumas limitações, como número limitado de túneis simultâneos.
3. **Uso nos próximos dias**: Estas funções serão utilizadas nos próximos dias do desafio quando for necessário expor aplicações locais (como o chatbot) para acesso externo.

## Próximos Passos
1. Atualizar o README.md do projeto com o progresso do Dia 2
2. Preparar-se para as tarefas do Dia 3 do desafio #7DaysOfCode
3. Integrar o Ngrok com as aplicações que serão desenvolvidas nos próximos dias

## Referências
- [Documentação oficial do Ngrok](https://ngrok.com/docs)
- [Documentação do PyNgrok](https://pyngrok.readthedocs.io/)