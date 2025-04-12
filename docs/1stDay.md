# Desenvolvimento de Chatbot em Python - Dia 1

## Configuração do Ambiente e Instalação de Pacotes

Este documento resume o processo de preparação do ambiente para um desafio de desenvolvimento de chatbot em Python em 7 dias, focando nas instruções para o primeiro dia.

### Configuração do GitHub

1. **Criar um novo repositório**
   - Acessar [github.com](https://github.com) e fazer login
   - Clicar em "+ > New repository"
   - Preencher detalhes: nome, descrição, marcar como público
   - Adicionar um README.md inicial

2. **Estrutura de pastas recomendada**
   - `/notebooks` - para notebooks do Colab
   - `/src` - para código fonte
   - `/docs` - para documentação

3. **Métodos para criar pastas no GitHub**
   - **Método 1:** Via interface web (criar arquivo com path incluindo o nome da pasta)
   - **Método 2:** Upload de arquivos organizados em pastas
   - **Método 3:** Via linha de comando Git (clone, mkdir, commit, push)
   - **Importante:** O GitHub não permite pastas vazias, sempre adicione pelo menos um arquivo (como README.md)

### Configuração do Google Colab

1. **Criar um novo notebook**
   - Acessar [colab.research.google.com](https://colab.research.google.com)
   - Criar novo notebook e renomear para "Dia1-Instalacao-Pacotes"

2. **Vincular ao GitHub**
   - Salvar uma cópia no GitHub no repositório criado

### Instalação dos Pacotes Requeridos

1. **Comando recomendado pelo instrutor (instalação em único comando)**
   ```python
   !pip3 install chainlit pyngrok langchain optimum auto-gptq --extra-index-url https://huggingface.github.io/autogptq-index/whl/cu118/ -q
   ```

2. **Abordagem alternativa (comandos separados)**
   ```python
   !pip install --upgrade pip
   !pip install chainlit
   !pip install pyngrok
   !pip install langchain
   !pip install optimum
   !pip install auto-gptq
   ```

3. **Verificação das instalações**
   ```python
   import chainlit
   from pyngrok import ngrok  # Correção: importar pyngrok em vez de ngrok
   import langchain
   import optimum  # Correção: importar optimum sem tentar acessar __version__
   
   # Verificando versões
   import pkg_resources
   print(f"Chainlit version: {chainlit.__version__}")
   print(f"Ngrok: Instalado com sucesso")
   print(f"Langchain version: {langchain.__version__}")
   optimum_version = pkg_resources.get_distribution("optimum").version
   print(f"Optimum version: {optimum_version}")
   print("Todas as bibliotecas foram instaladas com sucesso!")
   ```

### Diferenças entre as Abordagens de Instalação

1. **Instalação única vs. múltiplos comandos**
   - O instrutor usou um único comando para todas as bibliotecas
   - A abordagem alternativa usou comandos separados para cada biblioteca

2. **Índice adicional para AutoGPTQ**
   - O instrutor incluiu `--extra-index-url https://huggingface.github.io/autogptq-index/whl/cu118/`
   - Este índice fornece versões pré-compiladas otimizadas para CUDA 11.8
   - Importante mesmo no Colab para melhor desempenho com GPUs

3. **Outras diferenças**
   - Flag de silêncio (`-q`)
   - Atualização explícita do pip
   - Uso de `pip3` vs. `pip`

### Funções dos Pacotes Instalados

1. **Chainlit**
   - Framework para criação de interfaces de usuário para chatbots
   - Cria interfaces web interativas com elementos como inputs, botões e uploads

2. **PyNgrok (pyngrok)**
   - Wrapper Python para Ngrok, criando túneis seguros para servidores locais
   - Expõe aplicativos locais na internet através de URLs públicas

3. **LangChain**
   - Biblioteca para desenvolvimento de aplicações com modelos de linguagem
   - Fornece abstrações para interação com LLMs, gerenciamento de contexto e mais

4. **Optimum**
   - Biblioteca da Hugging Face para otimização de modelos
   - Oferece ferramentas para quantização e aceleração de inferência

5. **AutoGPTQ**
   - Biblioteca para quantização de modelos usando o método GPTQ
   - Reduz tamanho de modelos mantendo qualidade e acelera inferência

### Atualização do GitHub

- **Métodos para atualizar notebooks no GitHub**
  1. Salvar diretamente via Colab (Arquivo > Salvar uma cópia no GitHub)
  2. Download e upload manual
  3. Usando GitHub Desktop

### Próximos Passos

- Continuar com as próximas etapas do desafio de 7 dias
- Manter documentação atualizada no README.md
- Criar notebooks separados para cada dia do desafio