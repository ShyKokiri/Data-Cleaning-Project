<h1> Desafio: Limpeza e Padronização de Dados de Layoffs </h1>

<p align="center"> 📊 Projeto prático de SQL para tratamento de dados 📊 </P>
<p> Seja bem-vindo ao desafio: <strong>Limpeza e Padronização de Dados de Layoffs.</strong><br>
Desafio este inspirado em boas práticas de engenharia de dados e análise de dados. <br>
💎 O objetivo principal é colocar em prática habilidades de manipulação de dados no SQL: <strong>remoção de duplicatas, padronização, tratamento de valores nulos, conversão de datas e exclusão de colunas desnecessárias.</strong> </p>

---

<h2>🛑 Pré-Requisitos</h2>

<p>
✅ Conhecimento básico em SQL<br>
✅ MySQL instalado ou acesso a um ambiente compatível<br>
✅ Editor SQL (Workbench, DBeaver, etc.)<br>
✅ Git<br>
✅ Conta no GitHub para versionamento<br>
</p>

---

<h2> 👣 Passo-a-Passo</h2>

<p>
<strong>	1.</strong> Criar tabelas de estágio para preservar os dados originais<br>
<strong>	2.</strong> Identificar e remover duplicatas usando <code>ROW_NUMBER()</code><br>
<strong>	3.</strong> Padronizar colunas de texto (empresa, setor, país)<br>
<strong>	4.</strong> Converter datas do formato texto para <code>DATE</code><br>
<strong>	5.</strong> Tratar valores nulos ou em branco<br>
<strong>	6.</strong> Remover linhas e colunas desnecessárias<br>
</p>

---

<h2> 📚 Conceitos de Limpeza de Dados </h2>

<h3>🔺 Remoção de Duplicatas:</h3>
<p>
Usamos a função <code>ROW_NUMBER()</code> com <code>PARTITION BY</code> para identificar registros duplicados e removê-los.
</p>

WITH duplicate_cte AS (
  SELECT *,
    ROW_NUMBER() OVER(
      PARTITION BY company, location, industry, total_laid_off, percentage_laid_off,
                   `date`, stage, country, funds_raised_millions
    ) AS row_num
  FROM world_layoffs.layoffs_staging
)
DELETE
FROM world_layoffs.layoffs_staging2
WHERE row_num > 1;

<h3>🔺 Padronização de Dados:</h3> <p>Remoção de espaços extras e unificação de categorias.</p>
UPDATE world_layoffs.layoffs_staging2
SET company = TRIM(company),
    industry = TRIM(industry);

UPDATE world_layoffs.layoffs_staging2
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';

<h3>🔺 Conversão de Datas:</h3> <p>Transformação de strings para formato <code>DATE</code>.</p>
UPDATE world_layoffs.layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE world_layoffs.layoffs_staging2
MODIFY COLUMN `date` DATE;

<h3>🔺 Tratamento de Nulos:</h3> <p>Substituição de strings vazias por <code>NULL</code> e preenchimento com auto-joins quando possível.</p>
UPDATE world_layoffs.layoffs_staging2
SET industry = NULL
WHERE industry = '';

UPDATE world_layoffs.layoffs_staging2 t1
JOIN world_layoffs.layoffs_staging2 t2
  ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL
  AND t2.industry IS NOT NULL;

<h3>🔺 Exclusão de Colunas Desnecessárias:</h3> <p>Após o uso de <code>row_num</code> para remoção de duplicatas, a coluna foi eliminada.</p>
ALTER TABLE world_layoffs.layoffs_staging2
DROP COLUMN row_num;

<h2 align="center"> 📊 RESULTADO FINAL </h2> <p> A tabela <code>layoffs_staging2</code> final contém colunas padronizadas e limpas, prontas para análise:</p>

🔸company
🔸location
🔸industry
🔸total_laid_off
🔸percentage_laid_off
🔸date
🔸stage
🔸country
🔸funds_raised_millions
```

