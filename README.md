# Labb: Träna din egen AI med LoRA

I den här labben tränar du en språkmodell på ett eget fråge- och svars-dataset. Du använder **LoRA**, en metod som lägger små tränade anpassningar ovanpå en redan kunnig grundmodell. Resultatet blir en AI som bättre följer tonen och ämnet i dina exempel.

Arbeta i notebooken [unsloth_finetune_model.ipynb](unsloth_finetune_model.ipynb). Kör cellerna uppifrån och ned och vänta tills varje cell är klar innan du går vidare.

## Steg 1: Förbered verkstaden

Notebooken behöver bibliotek som Unsloth, Transformers och träningsverktyg.

1. Kontrollera att notebooken använder projektets virtuella miljö `.venv`.
2. Kör installationscellen med **▶ Run**.
3. Starta om kernel om VS Code eller Jupyter ber om det.

Om du installerar från terminalen ska du använda projektets Python, till exempel `.venv/bin/python`. Lägg inte `.venv` i GitHub-repot.

## Steg 2: Välj lärobok och grundmodell

I inställningscellen väljer du tre saker:

* `csv_fil`: CSV-filen som innehåller dina frågor och svar, till exempel `./dataset/gym_och_halsa.csv`.
* `ai_modell`: grundmodellen som ska tränas.
* `antal_traningssteg`: hur många uppdateringar modellen ska göra.

Byt `csv_fil` om du vill träna på ett annat ämne. Datasetet ska ligga i mappen `dataset` och ha kolumnerna `Instruction` och `Response`.

Kör inställningscellen med **▶ Run**. Kontrollera utskriften så att rätt dataset, modell och antal steg visas.

## Steg 3: Träna modellen

Träningscellen gör flera saker:

1. Läser in din CSV-fil.
2. Skapar en ny sessionsmapp under `training`.
3. Kontrollerar serverns GPU och väljer lämplig batchstorlek.
4. Laddar grundmodellen.
5. Formaterar varje exempel med `### Fråga:` och `### Svar:`.
6. Lägger till LoRA-anpassningar.
7. Tränar modellen och sparar checkpoints och adapterfiler.

Kör cellen med **▶ Run** och vänta tills meddelandet om att träningen är slutförd visas. Träning kan ta tid och loggen kan innehålla många rader.

## Vad är LoRA?

Grundmodellen har redan miljarder parametrar. I stället för att ändra alla tränas ett litet lager med nya parametrar. Du kan tänka på LoRA-lagret som Post-it-lappar på grundmodellens kunskap.

Det gör träningen snabbare och kräver mindre minne. Grundmodellen är kvar, medan adapterfilerna innehåller det som modellen lärde sig av ditt dataset.

## Steg 4: Exportera som GGUF

GGUF är ett modellformat som fungerar bra i program som LM Studio och Ollama.

Exportcellen:

1. Skapar en tillfällig arbetsmapp.
2. Slår ihop grundmodellen och LoRA-anpassningen.
3. Kvantiserar modellen till `q4_k_m` för att minska filstorleken.
4. Sparar den färdiga modellen i `gguf_export`.
5. Tar bort den tillfälliga mappen när exporten är klar.

Kör exportcellen med **▶ Run**. Den färdiga filen får ett namn som visar både dataset och att modellen är fintränad, till exempel:

`gym_och_halsa_finetuned_q4_k_m.gguf`

Om exportmappen redan finns kan cellen återanvända den befintliga GGUF-filen i stället för att skapa en ny export.

## Steg 5: Hitta resultatet

Varje körning får en egen mapp, till exempel:

`training/gym_och_halsa_2026-09-18_11-04-34/`

Där hittar du vanligtvis:

* `gguf_export/`: den färdiga GGUF-modellen för körning i LM Studio eller Ollama.
* `model/`: LoRA-adaptern och tokenizerfiler för fortsatt arbete.
* `checkpoints/`: sparade träningslägen som kan användas för att fortsätta träna.

`gguf_staging` är bara en tillfällig arbetsmapp och tas bort efter en lyckad export.

## Steg 6: Testa modellen

I testcellen ändrar du variabeln `test_fraga` till en egen fråga. Testa gärna både en fråga från datasetet och en helt ny fråga om samma ämne.

Exempel:

```python
test_fraga = "Vilken övning passar bäst för att träna ben?"
```

Kör cellen med **▶ Run** och läs svaret. Fundera på:

* Svarar modellen på rätt språk?
* Följer den tonen från dina träningsdata?
* Kan den svara på en omformulerad fråga?
* Hittar den på information som inte fanns i datasetet?

## Vad kan gå fel?

* **CSV-filen hittas inte:** Kontrollera sökvägen i `csv_fil` och att filen ligger i `dataset`.
* **Ingen CUDA-GPU hittas:** Träningscellen behöver en fungerande NVIDIA-GPU och rätt PyTorch-installation.
* **Unsloth saknas:** Kör installationscellen i rätt notebook-kernel.
* **Exportmappen finns redan:** Kör exportcellen igen. Den är gjord för att kunna återanvända en tidigare export.
* **Modellen svarar konstigt:** Ett litet dataset kan ge överinlärning. Testa fler och mer varierade frågor.

## Vad ska du lämna in eller dela?

För att bara köra modellen i LM Studio eller Ollama räcker normalt GGUF-filen i `gguf_export`.

Spara även `model`, `checkpoints` och datasetet om du vill fortsätta träna, undersöka resultatet eller återskapa exporten. Dela inte `.venv`, stora cachemappar eller onödiga träningsfiler i ett publikt GitHub-repo.

## Reflektion

LoRA ändrar inte grundmodellen till en helt ny AI. Du lär den ett särskilt mönster genom dina exempel. Därför påverkas resultatet mycket av hur tydliga, varierade och korrekta frågorna och svaren i datasetet är.

Jämför modellens svar före och efter träningen. Vad lärde den sig? Vad verkar den fortfarande ha svårt för? Det är nästa ledtråd till hur du kan förbättra datasetet.