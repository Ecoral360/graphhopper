## Auteur
Mathis Laroche

## Date
19 Novembre 2025

# Pourquoi on a changé le workflow ?

Avant, le workflow GitHub Actions faisait seulement tourner les tests de mutation (avec Pitest) pour donner un score.
Le problème, c’est que **le workflow ne vérifiait pas si le score devenait pire après un nouveau commit**.
Autrement dit, quelqu’un pouvait modifier du code, réduire la qualité des tests, et le pipeline continuait à dire « tout va bien ».

L’objectif a donc été de faire en sorte que :

1. **Le build échoue si le nouveau score de mutation est plus bas que l’ancien.**
   Ça permet de garantir qu’on ne diminue jamais la qualité du code ou des tests.

2. **On garde une trace du score précédent de la branche `main`.**
   Comme ça, une Pull Request peut comparer son propre score avec celui de la version de référence.

3. **On évite de relancer Pitest sur toute la branche `main` à chaque fois.**
   Ce serait trop long et ça coûterait du temps machine.
   On stocke juste un fichier texte généré par `main` qui contient le score.

# Comment on a validé ces changements ?

## 1. Test du workflow sur une branche de test

Avant de l’utiliser pour de vrai, on l’a lancé sur une branche séparée pour vérifier :

* que Pitest se lance correctement,
* qu’on arrive bien à extraire le score depuis le fichier XML,
* que le fichier texte `score.txt` est correctement sauvegardé en tant qu’artefact GitHub.

C’était surtout pour s’assurer que chaque étape fonctionne individuellement.

---

## 2. Vérifier que la comparaison fonctionne

On a fait plusieurs scénarios simples :

### **Cas 1 : Score identique**

* On a simulé un nouveau score exactement égal à celui stocké dans `score.txt`.
  Résultat attendu : **le workflow doit réussir**.

→ Ça marche : la comparaison considère que « égal » est acceptable.

### **Cas 2 : Score amélioré**

* Nouveau score plus haut que l’ancien.
  Résultat attendu : **le workflow passe**.

→ C’est bien ce qui se passe.

### **Cas 3 : Score diminué**

* Nouveau score volontairement plus bas.
  Résultat attendu : **le workflow échoue**, car on ne veut pas accepter une baisse de qualité.

→ Le workflow renvoie bien une erreur : `Mutation score decreased.`

---

## 3. Vérifier le comportement sur `master`

On a aussi vérifié que lorsqu’on pousse sur `master` :

* le score est correctement réenregistré,
* l’artefact GitHub appelé `mutation-score` se met à jour.

Cela garantit que les futures Pull Requests auront toujours une valeur de référence correcte.

---

## 4. Vérifier les cas limites

On a vérifié les cas où :

* aucun artefact n’existe encore (par exemple la toute première fois).
  → le workflow ne plante pas et continue normalement.
* Pitest ne génère pas le fichier attendu (échec des tests mutation).
  → le workflow plante avant d’en arriver à la comparaison, ce qui est normal.

---

# Conclusion

En gros, le workflow a été modifié pour :

* **empêcher la baisse de qualité des tests**,
* **stocker le score de mutation de manière fiable**,
* **éviter un coût inutile en performance**,
* **rendre la CI plus stricte mais pas plus lente**.
