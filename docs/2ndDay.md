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

### 6. Funções para gerenciamento de túneis Ngrok e servidores HTTP

#### Função para criar túnel com autenticação básica
```python
def criar_tunel_ngrok(porta, auth_token=None, usuario="admin", senha="chatbot123"):
    """
    Cria um túnel Ngrok para a porta especificada com autenticação básica.
    
    Args:
        porta (int): Porta local que você deseja expor.
        auth_token (str, opcional): Token de autenticação do Ngrok.
                                   Se não fornecido, solicita ao usuário.
        usuario (str, opcional): Nome de usuário para autenticação básica. Padrão: "admin"
        senha (str, opcional): Senha para autenticação básica. Padrão: "chatbot123"
    
    Returns:
        str: URL pública do túnel Ngrok.
    """
    from pyngrok import ngrok, conf
    import os
    
    # Verificar se o token está definido
    if not auth_token:
        if "NGROK_AUTH_TOKEN" not in os.environ:
            auth_token = input("Cole aqui seu token do Ngrok: ")
        else:
            auth_token = os.environ["NGROK_AUTH_TOKEN"]
    
    # Configurar o token
    os.environ["NGROK_AUTH_TOKEN"] = auth_token
    !ngrok authtoken {auth_token}
    
    # Configurar autenticação básica
    # O formato é: "usuario:senha"
    auth = f"{usuario}:{senha}"
    
    # Configurar e iniciar o túnel com autenticação básica
    pyngrok_config = conf.PyngrokConfig()
    public_url = ngrok.connect(
        addr=porta,
        auth=auth,
        pyngrok_config=pyngrok_config
    )
    
    print(f"Túnel Ngrok criado com sucesso!")
    print(f"URL pública: {public_url}")
    print(f"Credenciais de acesso:")
    print(f"  Usuário: {usuario}")
    print(f"  Senha: {senha}")
    
    return public_url
```

#### Função para encerrar túnel Ngrok
```python
def encerrar_tunel_ngrok(url_public):
    """
    Encerra um túnel Ngrok específico.
    
    Args:
        url_public (str): URL pública do túnel a ser encerrado.
    """
    from pyngrok import ngrok
    
    # Encerrar o túnel
    print("\nEncerrando o túnel...")
    ngrok.disconnect(url_public)
    ngrok.kill()
    print("Túnel encerrado!")
```

#### Função para iniciar um servidor HTTP de teste
```python
def iniciar_servidor_teste(porta=8000):
    """
    Inicia um servidor HTTP simples para testar o túnel Ngrok.
    
    Args:
        porta (int): Porta para iniciar o servidor. Padrão: 8000
        
    Returns:
        HTTPServer: O objeto do servidor iniciado
    """
    from http.server import HTTPServer, BaseHTTPRequestHandler
    import threading
    
    # Definir um manipulador HTTP simples
    class MeuManipulador(BaseHTTPRequestHandler):
        def do_GET(self):
            self.send_response(200)
            self.send_header('Content-type', 'text/html')
            self.end_headers()
            
            # Página HTML simples
            html = f"""
            <html>
              <head>
                <title>Teste do Chatbot - #7DaysOfCode</title>
                <style>
                  body {{ font-family: Arial, sans-serif; max-width: 800px; margin: 0 auto; padding: 20px; }}
                  h1 {{ color: #4285f4; }}
                  .container {{ border: 1px solid #ddd; padding: 20px; border-radius: 5px; }}
                </style>
              </head>
              <body>
                <h1>Servidor de Teste para o #7DaysOfCode</h1>
                <div class="container">
                  <h2>Chatbot em Python</h2>
                  <p>Se você está vendo esta página, significa que:</p>
                  <ul>
                    <li>O servidor HTTP está funcionando corretamente</li>
                    <li>O túnel Ngrok está encaminhando o tráfego adequadamente</li>
                    <li>A autenticação básica está funcionando</li>
                  </ul>
                  <p>Caminho atual: {self.path}</p>
                  <p>Endereço: {self.client_address}</p>
                </div>
              </body>
            </html>
            """
            
            self.wfile.write(html.encode())
    
    # Iniciar o servidor em uma thread separada
    server = HTTPServer(('', porta), MeuManipulador)
    print(f"Servidor iniciado na porta {porta}")
    print(f"Acesse em http://localhost:{porta} no ambiente local")
    print("Para encerrar o servidor, use a função finalizar_servidor()")
    
    # Executar o servidor em uma thread separada para não bloquear o notebook
    thread = threading.Thread(target=server.serve_forever)
    thread.daemon = True  # Permite que o notebook seja encerrado mesmo com o servidor rodando
    thread.start()
    
    return server
```

#### Função para finalizar o servidor HTTP
```python
def finalizar_servidor(servidor):
    """
    Finaliza um servidor HTTP iniciado pela função iniciar_servidor_teste.
    
    Args:
        servidor: Instância do servidor HTTP a ser finalizado.
    """
    if servidor:
        print("Finalizando o servidor HTTP...")
        servidor.shutdown()  # Encerra o loop serve_forever()
        servidor.server_close()  # Fecha o socket
        print("Servidor HTTP finalizado com sucesso.")
    else:
        print("Nenhum servidor para finalizar.")
```

### 7. Exemplo de uso completo

```python
# Iniciar servidor HTTP na porta 8000
servidor = iniciar_servidor_teste(porta=8000)

# Criar um túnel Ngrok para a porta 8000 com autenticação básica
url = criar_tunel_ngrok(
    porta=8000,
    usuario="admin",  # Você pode mudar para o usuário desejado
    senha="chatbot123"  # Você pode mudar para a senha desejada
)

# Aguardar enquanto você testa o acesso via URL do Ngrok
# Aqui você pode acessar a URL fornecida pelo Ngrok em qualquer navegador
# E inserir as credenciais de autenticação quando solicitado

# Quando terminar, finalize o servidor e o túnel
# finalizar_servidor(servidor)
# encerrar_tunel_ngrok(url)
```

## Observações Importantes
1. **Persistência do token**: A autenticação será necessária a cada nova sessão do Colab, já que o ambiente não mantém configurações entre sessões.
2. **Plano gratuito**: O plano gratuito do Ngrok tem algumas limitações, como número limitado de túneis simultâneos.
3. **Autenticação básica**: A autenticação HTTP básica fornece uma camada de segurança simples, mas não é tão robusta quanto OAuth (disponível apenas nos planos pagos).
4. **Encerramento de serviços**: Em algumas situações no Google Colab, pode ser necessário reiniciar o runtime para encerrar completamente os servidores (Menu > Runtime > Restart runtime).

## Resolução de Problemas

### Erro: "Connection refused"
Se você receber um erro como "connection refused" ao acessar a URL do Ngrok, isso geralmente significa que:
1. Não há nenhum serviço rodando na porta que você está tentando expor
2. O serviço está rodando em uma porta diferente da que você configurou no Ngrok

Solução: Certifique-se de iniciar primeiro o servidor HTTP na porta correta e só depois criar o túnel Ngrok para essa mesma porta.

## Próximos Passos
1. Atualizar o README.md do projeto com o progresso do Dia 2
2. Preparar-se para as tarefas do Dia 3 do desafio #7DaysOfCode
3. Integrar o Ngrok com as aplicações que serão desenvolvidas nos próximos dias

## Referências
- [Documentação oficial do Ngrok](https://ngrok.com/docs)
- [Documentação do PyNgrok](https://pyngrok.readthedocs.io/)