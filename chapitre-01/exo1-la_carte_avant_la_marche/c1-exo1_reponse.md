# La reponse a la commande Jenga info dans le dossier Nkentseu

**PS PS C:\Users\LENOVO\Desktop\Cours de l'ecole\Rihen univers\Nkentseu> jenga info > jenga_output.txt**

- **Fichier racine du workspace :** Nkentseu.jenga
- **Projet de demarrage :** Sandbox
- **Nombre de chaines de compilation :** 8
- **Nombre de projets par types :**
   - *ConsoleApp* : 98
      - *sortie:* **PS PS C:\Users\LENOVO\Desktop\Cours de l'ecole\Rihen univers\Nkentseu> Get-Content jenga_output.txt | Select-String "ConsoleApp" | Measure-Object | Select-Object -ExpandProperty Count
98**
   - *WindowedApp* : 55
      - *sortie:* **PS PS C:\Users\LENOVO\Desktop\Cours de l'ecole\Rihen univers\Nkentseu> Get-Content jenga_output.txt | Select-String "WindowedApp" | Measure-Object | Select-Object -ExpandProperty Count
55**
   - *StaticLib* : 60
      - *sortie:* **PS PS C:\Users\LENOVO\Desktop\Cours de l'ecole\Rihen univers\Nkentseu> Get-Content jenga_output.txt | Select-String "StaticLib" | Measure-Object | Select-Object -ExpandProperty Count
60**
   - *TestSuite* : 60
      - *sortie:* **PS PS C:\Users\LENOVO\Desktop\Cours de l'ecole\Rihen univers\Nkentseu> Get-Content jenga_output.txt | Select-String "TestSuite" | Measure-Object | Select-Object -ExpandProperty Count
60**
