# Como usar e atualizar o portefólio

O site é feito com **Jekyll**, que o GitHub Pages já sabe construir.
Não precisas de instalar nada: carregas os ficheiros para o GitHub e,
cerca de 1 minuto depois, o site está atualizado.

## Estrutura

```
_config.yml              ← o teu nome, título, links (LinkedIn, email, CV)
_data/certificates.yml   ← cursos e certificados (não aparecem no site)
_projects/               ← um ficheiro .md por projeto
assets/img/projects/     ← uma pasta de imagens por projeto
assets/cv/               ← o teu CV em PDF (opcional)
MODELO_PROJETO.md        ← modelo para copiar quando crias um projeto
```

Os ficheiros `_layouts/`, `assets/css/` e `index.html` definem o aspeto do
site. Normalmente não precisas de lhes mexer.

---

## 1. Publicar pela primeira vez

1. Entra no GitHub e cria um repositório novo (**New repository**).
   - Nome: **`O-TEU-UTILIZADOR.github.io`**, exatamente assim, com o teu
     nome de utilizador. Com este nome o site fica em
     `https://O-TEU-UTILIZADOR.github.io`.
   - Visibilidade: **Public**.
2. No repositório vazio, clica em **uploading an existing file**.
3. Arrasta **todo o conteúdo** da pasta `portfolio` (as pastas e os ficheiros,
   não a pasta `portfolio` em si) e clica em **Commit changes**.
4. Vai a **Settings → Pages** e confirma que a fonte é
   **Deploy from a branch**, ramo `main`, pasta `/ (root)`.
5. Espera 1–2 minutos e abre `https://O-TEU-UTILIZADOR.github.io`.
   O progresso aparece no separador **Actions**: verde significa publicado,
   vermelho significa erro (abre-o para ver a mensagem).

> As pastas que começam por `_` (como `_projects`) têm de ser carregadas.
> Se o upload as ignorar, carrega-as em separado, uma de cada vez.

## 2. Preencher os teus dados

Edita o `_config.yml` (no GitHub: abre o ficheiro → ícone do lápis):
`name`, `email`, `linkedin`, `github`, `cv_file`, `photo`.
Um campo vazio (`""`) esconde esse elemento no site.

- **CV:** põe o PDF em `assets/cv/CV.pdf` e escreve `cv_file: "/assets/cv/CV.pdf"`.
- **Foto:** põe-na em `assets/img/photo.jpg` e escreve `photo: "/assets/img/photo.jpg"`.

## 3. Adicionar um projeto novo (≈ 5 minutos)

1. Escolhe um nome curto para a pasta, sem espaços nem acentos
   (ex.: `heat-exchanger-fluent`). Esse nome também fica no endereço da página.
2. Copia o `MODELO_PROJETO.md` para `_projects/heat-exchanger-fluent.md`.
3. Cria a pasta `assets/img/projects/heat-exchanger-fluent/` e põe lá as imagens.
4. Preenche o topo do ficheiro (entre as linhas `---`) e o texto.
   No campo `cover`, põe o caminho da imagem principal.
5. Carrega os ficheiros para o GitHub. A página e o cartão na página inicial
   são criados sozinhos. Os projetos são ordenados pela `date`, com os mais
   recentes primeiro.

**Imagens dentro do texto:**

```markdown
![Descrição](/assets/img/projects/heat-exchanger-fluent/temperature.png)
*Temperature field at the outlet.*
```

A linha em itálico logo a seguir à imagem aparece como legenda.

**Dicas para as imagens:**
- PNG para capturas do Fluent/CFX e gráficos; JPG para fotografias.
- Largura de cerca de 1600 px chega. Tenta ficar abaixo de 1 MB por imagem.
- A imagem `cover` aparece cortada em 16:9 no cartão, por isso escolhe uma
  com o assunto ao centro.
- Fundo branco nos gráficos e eixos com unidades.

**Esconder um projeto** sem o apagar: acrescenta `published: false` no topo.

## 4. Certificados

A secção de certificados foi retirada do site. A lista continua guardada em
`_data/certificates.yml`. Se quiseres voltar a mostrá-la, pede ajuda para repor
a secção no `index.html` e o link no menu.

## 5. Comentários e rascunhos

O texto entre `<!--` e `-->` não aparece na página, mas **fica visível no
código-fonte do site e no repositório público**. Nunca escrevas lá notas
internas ou informação confidencial. Guarda as notas no `NOTAS_REVISAO.md`,
que fica só neste computador.

## Checklist de um bom projeto

- [ ] Uma frase de resumo que diz o resultado, não só o tema
- [ ] Imagem de capa apelativa (campo de velocidade, malha, linhas de corrente)
- [ ] Configuração clara: malha, modelos, condições de fronteira
- [ ] **Verificação ou validação**: estudo de malha, comparação com teoria ou com dados
- [ ] Limitações ditas com honestidade
- [ ] O que aprendeste
- [ ] Tudo em inglês
