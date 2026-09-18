# Finetuning med LoRA

När du finetunar med LoRA fryses Llama-modellens ursprungliga 8 miljarder vikter helt, medan ett tunt lager av nya anpassningsparametrar tränas på ditt dataset.

## Vad som händer under huven

* **Grundhjärnan är orörd:** Basmodellen ändras inte. Alla dess ursprungliga 8 miljarder kopplingar förblir låsta.
* **LoRA-adaptrar byggs:** Unsloth tränar bara ca 0,5 % av modellens storlek. Det är dessa små "Post-it-lappar" som lär sig mönstret i dina 70 frågor och svar.
* **Sammanslagning vid export:** När du exporterar till GGUF smälts basmodellen och dina tränade LoRA-lappar ihop till *en* ny, färdig modellfil.

## Skillnaderna du kommer att märka

* **Ny personlighet och ton:** Llama 3 slutar svara med sitt vanliga, lite långrandiga standardmönster. Den antar direkt den korta, svenska och ungdomliga tonen från din CSV-fil.
* **Formatsäkerhet:** Modellen blir expert på att lyda din mall (`### Fråga:` och `### Svar:`) och stannar direkt med `eos_token` när svaret är klart, utan att svamla vidare.
* **Pricksäkra domänsvar:** Ställer du frågor från ditt dataset (t.ex. om PPL, kreatin eller gymetikett) svarar den med exakt den fakta och attityd du har lärt den.

## Vad som INTE ändras (och vad du ska se upp med)

* **Allmänkunskapen finns kvar:** Om du ställer en helt orelaterad fråga (t.ex. "Vem var Gustav Vasa?") kan den fortfarande svara eftersom Llama 3:s grundläggande kunskapsbas inte har raderats.
* **Risk för utantillinlärning (Overfitting):** På ett dataset med 70 exempel lär sig modellen dina svar nästan utan till. Om du ställer en fråga exakt som i CSV-filen svarar den perfekt. Om du omformulerar frågan kraftigt kommer den fortfarande att försöka vinkla svaret mot dina träningsexempel.

Kort sagt: du förvandlar en generell, engelsktalande superdator till en specialiserad, svensktalande "gymkompis" eller "skolbot"!