# Image Pipeline para Opalescencia Studio

Pipeline automático para gerar vídeos em lote a partir de prompts (Stable Diffusion + MoviePy).

## Workflow Rápido (Recomendado)

### 1. Gerar imagens + montar vídeo no Colab
1. Abra [`generate_images_colab.ipynb`](./generate_images_colab.ipynb) no [Google Colab](https://colab.research.google.com/).
2. **Runtime → Change runtime type → GPU** (requer Colab Pro para GPU melhor ou T4 grátis).
3. Execute as células na ordem:
   - **Instalar deps** (pip install)
   - **HF Token** (se usar modelo privado, cole seu token do Hugging Face)
   - **Parâmetros de geração**: mude `GENERATE_FULL_BATCH = True` e `FULL_BATCH_SIZE = 120` para gerar ~120 imagens (~8 min de vídeo).
   - **Gerar imagens** → **Upscale** → **Montagem** → **Upload para Drive** (automático se Drive montado).
4. Verifique o resultado em `/content/drive/MyDrive/Opalescencia_Studio_outputs/final_video.mp4`.

### 2. Montar vídeo localmente (sem Colab)
Se você já tem imagens em `cenas/`:
```bash
cd /home/surama/Documentos/Projetos/Negócios\ Digitais/Canais\ Dark/Opalescencia_Studio
python3 montar_video.py
```
Resultado: `videos_finais/video_final.mp4`

## Estrutura

- `generate_images_colab.ipynb` – Notebook completo (geração, upscale, montagem, Drive upload).
- `prompts_first_minute.txt` – Prompts para os primeiros 60s de um vídeo (edite para ajustar o estilo).
- `../montar_video.py` – Script de montagem (usado também no notebook).

## Automação para 3 Vídeos/Dia

### Opção A: Colab manualmente (GRATUITO, mais lento)
- Execute o notebook 3 vezes (uma vez por vídeo) com `FULL_BATCH_SIZE = 120`.
- Tempo: ~30 min por vídeo em T4 grátis; ~15 min em GPU Pro (Colab Pro = $10/mês).

### Opção B: VM com GPU (MAIS RÁPIDO, custo recorrente)
Estimativa mensal para produção contínua 3 vídeos/dia (~90 vídeos/mês):

| Provedor | VM + GPU | Tempo/vídeo | Custo/mês est. | Nota |
|----------|----------|-------------|----------------|------|
| **Google Cloud** | e2-medium + T4 | 12–15 min | $180–220 | Spot: $80–120 |
| **AWS** | t3.medium + Tesla T4 | 12–15 min | $200–250 | Spot: $100–130 |
| **Paperspace** | P4000 (shared) | 8–10 min | $15–30 | Melhor relação; escalável |
| **Lambda Labs** | GPU shared | 10–12 min | $25–50 | Simples, sem contrato |
| **Local (seu PC)** | RTX 3060 ou melhor | 5–8 min | $0 (elétrica) | Setup único; sem limites |

**Recomendação**: Comece com Colab (grátis + manual) para validar o estilo. Depois, se escala regularmente, migre para **Paperspace** (mais barato + automação fácil via cron).

## Parâmetros Ajustáveis

No notebook, antes de "Run all":
- `GENERATE_FULL_BATCH`: True/False → gera 10 ou N imagens.
- `FULL_BATCH_SIZE`: número de imagens (cada ~6s no vídeo final).
- `WIDTH, HEIGHT`: resolução da geração (1280x720 = rápido; 1920x1080 = qualidade).
- `USE_REAL_ESRGAN`: True/False → upscale de qualidade (mais lento) vs Lanczos rápido.
- **Prompts**: edite as 10 linhas em `prompts_first_minute.txt` para mudar o estilo.

## Troubleshooting

- **Colab timeout**: se exceder 12h, salve as imagens em Drive e rode montagem em outra sessão.
- **MoviePy error**: use `python3 -c "import moviepy; print(moviepy.__version__)"` para verificar versão.
- **Espaço em Drive**: vídeos finais ~1.5–3M cada; imagens upscaled ~1M cada. 120 imagens ≈ 120M.

## Próximas Melhorias (Opcional)

- Integrar narração (áudio + sync com MP4).
- Cron job em VM para gerar automático 3x/dia.
- Dashboard web para acompanhar progresso e fazer download.
