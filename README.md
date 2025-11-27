<p align="center">
  <img src="https://raw.githubusercontent.com/SEU_USUARIO/SEU_USUARIO/main/assets/banner.svg" width="100%">
</p>
## 📊 Estatísticas do GitHub

<p align="center">
  <img height="180em"
       src="https://github-readme-stats.vercel.app/api?username=SEU_USUARIO&show_icons=true&theme=radical&include_all_commits=true&count_private=true"/>
  <img height="180em"
       src="https://github-readme-stats.vercel.app/api/top-langs/?username=SEU_USUARIO&layout=compact&theme=radical"/>
</p>
<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com?font=Fira+Code&size=30&pause=800&color=8B5CF6&center=true&vCenter=true&width=600&lines=Backend+Developer;Python+%7C+FastAPI;SQL+%7C+NoSQL;Google+Cloud;Criando+APIs+Profissionais">
</p>
<p align="center">
  <img src="https://media.giphy.com/media/l3vR85PnGsBwu1PFK/giphy.gif" width="300">
</p>
<p align="center">
  <img src="https://profile-counter.glitch.me/SEU_USUARIO/count.svg">
</p>
## 🔥 Meus Projetos

<!-- PROJECTS-LIST:START -->
<!-- PROJECTS-LIST:END -->
import requests
import datetime

USERNAME = "lucasam87"
URL = f"https://api.github.com/users/{USERNAME}/repos?sort=updated&per_page=100"

def fetch_repos():
    response = requests.get(URL)
    repos = response.json()
    return [
        {
            "name": repo["name"],
            "url": repo["html_url"],
            "desc": repo["description"] if repo["description"] else "Sem descrição",
            "stars": repo["stargazers_count"],
        }
        for repo in repos
    ]

def format_repo(repo):
    return f"- [{repo['name']}]({repo['url']}) • ⭐ {repo['stars']} • {repo['desc']}"

def update_readme():
    with open("README.md", "r", encoding="utf-8") as file:
        content = file.read()

    start_tag = "<!-- PROJECTS-LIST:START -->"
    end_tag = "<!-- PROJECTS-LIST:END -->"

    start_index = content.index(start_tag) + len(start_tag)
    end_index = content.index(end_tag)

    repos = fetch_repos()
    formatted = "\n".join(format_repo(r) for r in repos)

    new_content = content[:start_index] + "\n" + formatted + "\n" + content[end_index:]

    with open("README.md", "w", encoding="utf-8") as file:
        file.write(new_content)

update_readme()
name: Atualizar projetos

on:
  schedule:
    - cron: "0 */12 * * *"  # Atualiza 2x por dia
  workflow_dispatch:

jobs:
  update-projects:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.x"

      - name: Install dependencies
        run: pip install requests

      - name: Update projects section
        run: python .github/scripts/update_projects.py

      - name: Commit and push changes
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add README.md
          git commit -m "Atualização automátizada dos projetos" || echo "Sem mudanças"
          git push
