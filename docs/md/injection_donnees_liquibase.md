# Injecter des données à l'installation via liquibase

À chaque version, on peut fournir des données à injecter dans la base de données via le script d'installation de la version.

Pour ce faire, ces données doivent être dans le dossier data de la version et le master data appelé dans le master de la version.



## Structure du liquibase

```bash
-aps-backend/src/main/resources/db.changelog/1.0.0
	|-database
		|-1.0.0 ................................................. Décrit la version 1.0.0 de la base de données
		    |-data .................................................. Dossier contenant les données
		        |-00.references ......................................... Création des valeurs de références
		            |-db.changelog-data-entrantes.xml ....................... 1 fichier par groupe fonctionnel
		            |-db.changelog-data-ouvrages.xml
		            |-master.xml
		        |-01.users .............................................. Création des users
		            |-admins-data.xml ....................................... le fichier contient tous les users de rôle admin, etc
		            |-experts-data.xml
		            |-clients-data.xml
		            |-master.xml
		        |-02.ouvrages ........................................... Création des ouvrages
		            |-ouvrage001.xml ........................................ 1 fichier contient un ouvrage
		            |-ouvrage002.xml
		            |-master.xml
		        |-03.usr-oat............................................. Création des liens entre user et ouvrages
		            |-usr-oat001.xml ........................................ 1 fichier contient tous les liens users pour un ouvrage
		            |-usr-oat002.xml
		            |-master.xml
		        |-04.modes .............................................. Création des modes
		            |-modes-ouvrage001.xml .................................. 1 fichier contient les modes d un ouvrage
		            |-modes-ouvrage002.xml
		            |-master.xml
		        |-05.capteurs ........................................... Création des capteurs
		            |-capteurs-ouvrage001.xml ............................... 1 fichier contient les capteurs d un ouvrage
		            |-capteurs-ouvrage002.xml
		            |-master.xml
		        |-06.politiques ......................................... Création des politiques de surveillance
		            |-ouvrage001-psu001.xml ................................. 1 fichier contient une politique de surveillance
		            |-ouvrage001-psu002.xml
		            |-master.xml
		        |-07.seuils ............................................. Création des seuils
		            |-ouvrage001-psu001.xml ................................. 1 fichier contient le seuil d une politique
		            |-ouvrage002-psu001.xml
		            |-master.xml
		        |-08.details-seuils ..................................... Création des détails seuils
		            |-ouvrage001-psu001.xml ................................. 1 fichier contient le détails des seuils d une politique
		            |-ouvrage002-psu001.xml
		            |-master.xml
		        |-09.phis ............................................... Création des ratios phis
		            |-ouvrage001-psu001.xml ................................. 1 fichier contient les ratios phis d une politique
		            |-ouvrage002-psu001.xml
		            |-master.xml
		        |-10.alphas ............................................. Création des ratios alphas
		            |-ouvrage001-psu001.xml ................................. 1 fichier contient les ratios alphas d une politique
		            |-ouvrage002-psu001.xml
		            |-master.xml
		        |-11.etats .............................................. Création des états
		            |-ouvrage001-etat001.xml ................................ 1 fichier contient un état d un ouvrage
		            |-ouvrage001-etat002.xml
		            |-ouvrage002-etat001.xml
		            |-master.xml
		        |-12.donnees-modes ...................................... Création des données par mode
		            |-ouvrage001-etat001.xml ................................ 1 fichier contient les donnees pour tous les modes d un etat
		            |-ouvrage001-etat002.xml
		            |-master.xml
		        |-13.donnees-capteurs ................................... Création des données par capteur
		            |-ouvrage001-etat001.xml ................................ 1 fichier contient toutes les données capteurs d un état
		            |-ouvrage001-etat002.xml
		            |-master.xml
		        |-14.kpi-locaux ......................................... Création des kpi-locaux
		            |-data-ouvrage001-etat001.xml ........................... 1 fichier contient les kpi locaux pour un état
		            |-data-ouvrage001-etat002.xml
		            |-master.xml
		        |-15.mac ................................................ Création des macs
		            |-data-ouvrage001-etat001.xml ........................... 1 fichier contient la mac pour un état
		            |-data-ouvrage001-etat002.xml
		            |-master.xml
		        |-16.comac .............................................. Création des comacs
		            |-data-ouvrage001-etat001.xml ........................... 1 fichier contient la comac pour un etat
		            |-data-ouvrage001-etat002.xml
		            |-master.xml
		    |-struct ................................................ Dossier contenant les créations / modifications de tables et index
		        |-indexes ............................................... Création des index
		            |-db.changelog-data-entrantes.xml ....................... 1 fichier par groupe fonctionnel
		            |-db.changelog-data-ouvrages.xml
		            |-master.xml
		        |-tables ................................................ Création des tables
		            |-db.changelog-data-entrantes.xml ....................... 1 fichier par groupe fonctionnel
		            |-db.changelog-data-ouvrages.xml
		            |-master.xml
		        |-db.changelog-master.xml ............................... Master de la version pour la structure
		        |-db.changelog-master-data.xml .......................... Master de la version pour les données
        |-db.changelog-master.xml ............................... Master global, permet de monter la base from scratch en dev
        |-db.property.xml ....................................... Contient les propriétés utilisées partout (taille des champs, tablespaces)
    |-generateBdd.sh ........................................ Script de génération du sql pour la structure et les données de la version
    |-generateDataProd.sh ................................... Script de génération des données pour le démarrage en prod de la v1
```

## Règles de base lors de l'écriture de fichiers pour insertion de données
Ces lignes directrices sont à suivre pour tous les fichiers.

- [ ] Respecter la structure de dossiers. Elle est mise en place pour générer automatiquement les scripts SQL aux normes Apave (une table = un script)
- [ ] Toujours préciser le type des valeurs des colonnes dans la balise en suivant les précos de la partie [types de données](#types-de-donnees)
- [ ] Sauf données de tests explicites (essayer de le préciser dans les données de manière visible pour l'utilisateur), remplir les données insérées de manière valide, pour éviter les anomalies dûes à des données incohérentes.



## Types de donnees
### String / VARCHAR2
```xml
<!-- Non -->
<insert tableName="ref_mdc_mode_calcul">
  <column name="mdc_mode">ABSOLU</column>
</insert>
<!-- Oui -->
<insert tableName="ref_mdc_mode_calcul">
  <column name="mdc_mode" value="ABSOLU"/>
</insert>
```


### Date
```xml
<!-- Non -->
<insert tableName="t_oat_ouvrage_art">
  <column name="oatdt_chgt_mode_surv">2021-02-01</column>
</insert>
<!-- Oui -->
<insert tableName="t_oat_ouvrage_art">
  <column name="oatdt_chgt_mode_surv" valueDate="2021-02-01"/>
</insert>
```

### Numérique
```xml
<!-- Non -->
<insert tableName="t_psu_politique_surveillance">
  <column name="psunb_rho_comac" value="0.5"/>
</insert>
<!-- Oui -->
<insert tableName="t_psu_politique_surveillance">
  <column name="psunb_rho_comac" valueNumeric="0.5"/>
</insert>
```

### Booléen
```xml
<!-- Non -->
<insert tableName="t_psu_politique_surveillance">
  <column name="psubo_actif" value="true"/>
</insert>
<!-- Oui -->
<insert tableName="t_psu_politique_surveillance">
  <column name="psubo_actif" valueBoolean="true"/>
</insert>
```

### Sous-requête
```xml
<!-- Non -->
<insert tableName="t_psu_politique_surveillance">
  <column name="oat_id" valueComputed="select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001'"/>
</insert>
<!-- Oui: on notera les parenthèses autour de la requête -->
<insert tableName="t_psu_politique_surveillance">
  <column name="oat_id" valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
</insert>
```

### Requêtes natives
```xml
<!-- Non -->
<update tableName="t_eta_etat">
  <column name="eta_id_reference"
          valueComputed="select eta_id from t_eta_etat
            where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')
            and etanb_numero_etat = 1"/>
  <where>eta_id=(select eta_id from t_eta_etat
    where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')
    and etanb_numero_etat = 1)</where>
</update>
<!-- Oui: on notera la suppression des retours à la ligne dans le where -->
<update tableName="t_eta_etat">
<column name="eta_id_reference"
        valueComputed="select eta_id from t_eta_etat
            where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')
            and etanb_numero_etat = 1"/>
<where>eta_id=(select eta_id from t_eta_etat where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and etanb_numero_etat = 1)</where>
</update>
```


## Description des fichiers d'insert de données

### Références
Exemple d'insertion de valeurs de références
```xml
<changeSet author="Alteca" id="ref-mode-calcul">
  <insert tableName="ref_mdc_mode_calcul">
    <column name="mdc_mode">ABSOLU</column>
  </insert>
  <insert tableName="ref_mdc_mode_calcul">
    <column name="mdc_mode">RELATIF</column>
  </insert>
</changeSet>
```
#### Règles à suivre:
- [ ] Ne mettre que les valeurs de références dans ces fichiers.
- [ ] Grouper les valeurs de ref par groupe fonctionnel


### Users
Exemple du contenu d'un fichier se trouvant dans 01.users
```xml
<changeSet id="user-initialisation-donnees-4" author="Alteca">
  <insert tableName="t_usr_user">
    <column name="usrva_identifiant" value="expert"/>
    <column name="usrva_password"
      value="$2y$10$LKyOmLpPcdxOEqNB8k1UdOnqLY4wQPfGAVa4s1r9pNBRPw.gKttVe"/> <!-- 123456 -->
    <column name="usrva_role" value="EXPERT"/>
  </insert>
</changeSet>
```

#### Règles à suivre:
- [ ] Mettre le mot de passe en commentaire pour ne pas le perdre (pour les users de test seulement)
- [ ] Grouper les users par rôle dans leurs fichiers respectifs

### Ouvrages
Exemple du contenu d'un fichier se trouvant dans le dossier 02.ouvrages

```xml
<changeSet id="ouvrage-art-1" author="Alteca">
  <insert tableName="t_oat_ouvrage_art">
    <column name="oatva_identifiant" value="OA-OK-001"/>
    <column name="oatdt_chgt_mode_surv" valueDate="2021-02-01"/>
    <column name="mdc_mode" value="ABSOLU"/>
    <column name="msu_mode" value="STANDARD"/>
    <column name="oatva_id_externe" value="OA-OK-001-ext"/>
    <column name="dt_date_creation" valueComputed="CURRENT_TIMESTAMP"/>
    <column name="dt_date_modification" valueComputed="CURRENT_TIMESTAMP"/>
  </insert>
</changeSet>
```

### Lien users-ouvrage
Exemple du contenu d'un fichier se trouvant dans le dossier 03.usr-oat

```xml
<changeSet id="ouvrage-art-1" author="Alteca">
  <!-- visibilité oat OA-OK-001 sur user client -->
  <insert tableName="r_usr_oat">
    <column name="usr_id"
      valueComputed="(select usr_id from t_usr_user where usrva_identifiant = 'client')"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
  </insert>
</changeSet>
```
#### Règles à suivre:
- [ ] Grouper la visibilité d'un ouvrage dans un fichier


### Modes
Exemple du contenu d'un fichier se trouvant dans le dossier 04.modes

```xml
<changeSet id="modes-ouvrage-art-1" author="Alteca">
  <insert tableName="t_mod_mode">
    <column name="oat_id" valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="1"/>
    <column name="sercel_mod_numero" valueNumeric="1"/>
  </insert>
  <insert tableName="t_mod_mode">
    <column name="oat_id" valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="2"/>
    <column name="sercel_mod_numero" valueNumeric="2"/>
  </insert>
</changeSet>

```
#### Règles à suivre:
- [ ] Modes d'un seul ouvrage dans un fichier
- [ ] Nommer le fichier de manière à ce qu'on retrouve facilement les infos

### Capteurs
Exemple du contenu d'un fichier se trouvant dans le dossier 05.capteurs

```xml
<changeSet id="ouvrage-art-1" author="Alteca">
  <insert tableName="t_cap_capteur">
    <column name="oat_id" valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="cap_numero" valueNumeric="1"/>
  </insert>
  <insert tableName="t_cap_capteur">
    <column name="oat_id" valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="cap_numero" valueNumeric="2"/>
  </insert>
</changeSet>
```
#### Règles à suivre:
- [ ] Capteurs d'un seul ouvrage dans un fichier
- [ ] Nommer le fichier de manière à ce qu'on retrouve facilement les infos


### Politiques de surveillance
Exemple du contenu d'un fichier se trouvant dans 06.politiques

```xml
<changeSet id="politique-surveillance1-ouvrage1" author="Alteca">
  <insert tableName="t_psu_politique_surveillance">
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="psubo_actif" valueBoolean="false"/>
    <column name="psunb_rho_comac" valueNumeric="0"/>
    <column name="psunb_rho_kpi_locaux" valueNumeric="0"/>
    <column name="psunb_beta_l1" valueNumeric="0"/>
    <column name="psunb_beta_l2" valueNumeric="0"/>
    <column name="psunb_beta_l3" valueNumeric="0"/>
    <column name="psunb_beta_l4" valueNumeric="0"/>
    <column name="psunb_beta_l5" valueNumeric="0"/>
    <column name="dt_date_creation" valueComputed="CURRENT_TIMESTAMP"/>
    <column name="dt_date_modification" valueComputed="CURRENT_TIMESTAMP"/>
  </insert>
</changeSet>
```

### Seuils
Exemple du contenu d'un fichier se trouvant dans 07.seuils

```xml
<changeSet id="politique-surveillance1-ouvrage1" author="Alteca">
  <insert tableName="t_sls_seuils">
    <column name="psu_id"
      valueComputed="(select psu_id from t_psu_politique_surveillance
          where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)"/>
    <column name="slsnb_global_s1" valueNumeric="0"/>
    <column name="slsnb_global_s2" valueNumeric="0"/>
    <column name="slsnb_global_s3" valueNumeric="0"/>
    <column name="slsnb_global_s4" valueNumeric="0"/>
    <column name="dt_date_creation" valueComputed="CURRENT_TIMESTAMP"/>
    <column name="dt_date_modification" valueComputed="CURRENT_TIMESTAMP"/>
  </insert>
</changeSet>
```
#### Règles à suivre:
- [ ] Le seuil parent d'une seule politique de surveillance par fichier

### Details-seuils
Exemple du contenu d'un fichier se trouvant dans 08.details-seuils

```xml
<changeSet id="politique-surveillance1-ouvrage1" author="Alteca">
  <insert tableName="t_dsl_details_seuils">
    <column name="sls_id"
      valueComputed="(select sls_id from t_sls_seuils where psu_id = ((select psu_id from t_psu_politique_surveillance
          where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)))"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="1"/>
    <column name="tkp_type" value="FREQUENCE"/>
    <column name="dslnb_l1_absolu" valueNumeric="0.20"/>
    <column name="dslnb_l1_relatif" valueNumeric="0.05"/>
    <column name="dslnb_l2_absolu" valueNumeric="0.60"/>
    <column name="dslnb_l2_relatif" valueNumeric="0.10"/>
    <column name="dslnb_l3_absolu" valueNumeric="1.2"/>
    <column name="dslnb_l3_relatif" valueNumeric="0.15"/>
    <column name="dslnb_l4_absolu" valueNumeric="1.8"/>
    <column name="dslnb_l4_relatif" valueNumeric="0.20"/>
  </insert>
  .
  .
  .
</changeSet>
```
#### Règles à suivre:
- [ ] Les détails seuils associés à un seul seuil parent par fichier

### Ratios sur la comac: Phi
Exemple du contenu d'un fichier se trouvant dans 09.phis
```xml
<changeSet id="politique-surveillance1-ouvrage1" author="Alteca">
  <insert tableName="t_phi_ratio_phi">
    <column name="psu_id"
      valueComputed="(select psu_id from t_psu_politique_surveillance where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="cap_numero" valueNumeric="1"/>
    <column name="phinb_ratio" valueNumeric="0.5"/>
  </insert>
  <insert tableName="t_phi_ratio_phi">
    <column name="psu_id"
      valueComputed="(select psu_id from t_psu_politique_surveillance where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="cap_numero" valueNumeric="2"/>
    <column name="phinb_ratio" valueNumeric="0.5"/>
  </insert>
</changeSet>
```
#### Règles à suivre:
- [ ] Les phis d'une seule PSU par fichier

### Ratios sur les kpis locaux: Alpha
Exemple du contenu d'un fichier se trouvant dans 10.alphas
```xml
<changeSet id="politique-surveillance1-ouvrage1" author="Alteca">
  <insert tableName="t_alp_ratio_alpha">
    <column name="psu_id"
      valueComputed="(select psu_id from t_psu_politique_surveillance where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="1"/>
    <column name="alpnb_frequence" valueNumeric="0.40"/>
    <column name="alpnb_amortissement" valueNumeric="0.10"/>
    <column name="alpnb_mcf" valueNumeric="0.05"/>
    <column name="alpnb_mac" valueNumeric="0.45"/>
  </insert>
  <insert tableName="t_alp_ratio_alpha">
    <column name="psu_id"
      valueComputed="(select psu_id from t_psu_politique_surveillance where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="2"/>
    <column name="alpnb_frequence" valueNumeric="0.40"/>
    <column name="alpnb_amortissement" valueNumeric="0.10"/>
    <column name="alpnb_mcf" valueNumeric="0.05"/>
    <column name="alpnb_mac" valueNumeric="0.45"/>
  </insert>
</changeSet>
```
#### Règles à suivre:
- [ ] Les alphas d'une seule PSU par fichier

### Etats
Exemple du contenu d'un fichier se trouvant dans 11.etats

```xml
<changeSet id="etat1-ouvrage1" author="Alteca">
  <insert tableName="t_eta_etat">
    <column name="etadt_date_mesure" valueDate="2021-02-20"/>
    <column name="msu_mode" value="STANDARD"/>
    <column name="etabo_reference" valueBoolean="true"/>
    <column name="etanb_numero_etat" valueNumeric="1"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="psu_id"
      valueComputed="(select psu_id from t_psu_politique_surveillance
          where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and psubo_actif = 0)"/>
    <column name="etanb_kpi_global_valeur_moy" valueNumeric="0.03"/>
    <column name="niv_niveau_kpi_global_moy" value="L1"/>
    <column name="ste_statut" value="OK"/>
    <column name="dt_date_creation" valueComputed="CURRENT_TIMESTAMP"/>
    <column name="dt_date_modification" valueComputed="CURRENT_TIMESTAMP"/>
    <column name="mdc_mode" value="ABSOLU"/>
  </insert>
  <update tableName="t_eta_etat">
    <column name="eta_id_reference"
      valueComputed="(select eta_id from t_eta_etat where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and etanb_numero_etat = 1)"/>
    <where>eta_id=(select eta_id from t_eta_etat where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and etanb_numero_etat = 1)
    </where>
  </update>
</changeSet>
```
#### Règles à suivre:
- [ ] Un état par fichier

### Données mode
Exemple du contenu d'un fichier se trouvant dans 12.donnees-modes
```xml
<changeSet id="etat1-ouvrage1" author="Alteca">
  <insert tableName="t_dnm_donnees_mode">
    <column name="eta_id"
      valueComputed="(select eta_id from t_eta_etat where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and etanb_numero_etat = 1)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="1"/>
    <column name="dnmnb_frequence" valueNumeric="1.25"/>
    <column name="dnmnb_amortissement" valueNumeric="0.35"/>
    <column name="dnmnb_mcf" valueNumeric="35.25"/>
  </insert>
  .
  .
  .
</changeSet>
```
#### Règles à suivre:
- [ ] Les données modes d'un seul état par fichier

### Données capteurs
Exemple du contenu d'un fichier se trouvant dans 13.donnees-capteurs

```xml
<changeSet id="etat1-ouvrage1" author="Alteca">
  <insert tableName="t_dnc_donnees_capteur">
    <column name="dnm_id"
      valueComputed="(select dnm_id from t_dnm_donnees_mode
          where eta_id = (select eta_id from t_eta_etat
            where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')
            and etanb_numero_etat = 1)
          and mod_numero = 1)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="cap_numero" valueNumeric="1"/>
    <column name="dncnb_x" valueNumeric="2"/>
    <column name="dncnb_y" valueNumeric="5"/>
    <column name="dncnb_z" valueNumeric="3"/>
  </insert>
  .
  .
  .
</changeSet>
```
#### Règles à suivre:
- [ ] Les données capteur d'un seul état par fichier

### Kpi locaux
Exemple du contenu d'un fichier se trouvant dans 14.kpi-locaux
```xml
<changeSet id="data-calc-etat1-ouvrage1" author="Alteca">
  <insert tableName="t_kpl_kpi_local">
    <column name="eta_id"
      valueComputed="(select eta_id from t_eta_etat where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001') and etanb_numero_etat = 1)"/>
    <column name="oat_id"
      valueComputed="(select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')"/>
    <column name="mod_numero" valueNumeric="1"/>
    <column name="tkp_type" value="FREQUENCE"/>
    <column name="kplnb_valeur" valueNumeric="0.12"/>
    <column name="niv_niveau" value="L1"/>
  </insert>
  .
  .
  .
</changeSet>
```
#### Règles à suivre:
- [ ] Les kpi locaux d'un seul état par fichier

### Macs
Exemple du contenu d'un fichier se trouvant dans 15.mac
```xml
<changeSet id="data-calc-etat1-ouvrage1" author="Alteca">
  <insert tableName="t_mac_mac">
    <column name="eta_id"
      valueComputed="(select eta_id from t_eta_etat
            where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')
            and etanb_numero_etat = 1)"/>
    <column name="mac_x" valueNumeric="1"/>
    <column name="mac_y" valueNumeric="1"/>
    <column name="macnb_valeur" valueNumeric="0.98"/>
    <column name="niv_niveau" value="L1"/>
  </insert>
  .
  .
  .
</changeSet>
```
#### Règles à suivre:
- [ ] La mac d'un seul état par fichier

### Comacs
Exemple du contenu d'un fichier se trouvant dans 16.comacs
```xml
<changeSet id="data-calc-etat1-ouvrage1" author="Alteca">
  <insert tableName="t_com_comac">
    <column name="eta_id"
      valueComputed="(select eta_id from t_eta_etat
            where oat_id = (select oat_id from t_oat_ouvrage_art where oatva_identifiant = 'OA-OK-001')
            and etanb_numero_etat = 1)"/>
    <column name="com_x" valueNumeric="1"/>
    <column name="comnb_valeur" valueNumeric="0.02"/>
    <column name="niv_niveau" value="L1"/>
  </insert>
  .
  .
  .
</changeSet>
```
#### Règles à suivre:
- [ ] La comac d'un seul état par fichier


## Génération des scripts SQL

La structure des dossiers permet à un script bash de générer les fichiers SQL de la version dans des dossiers séparés:

```bash
|- oracle
    |-1.0.0
        |-schemas
            |-APS
                |-00-Drop-Database.sql
                |-01-Create-Tables.sql
                |-02-Create-Indexes.sql
        |-datas
            |-APS
                |-00-Insert-references.sql
                |-01-Insert-users.sql
                ...
                |-16-Insert-comac.Sql
```

### Génération de la partie schéma:

#### 00-Drop-Database.sql
Utilisation du goal maven liquibase:futureRollbackSQL qui génère un script sql basé sur les balises rollback de tous les changeset du master passé en argument.

```bash
mvn -Ptest liquibase:futureRollbackSQL -Dliquibase.url=offline:oracle -Dliquibase.changeLogFile=db/changelog/1.0.0/struct/tables/master.xml -Dliquibase.changeLogDirectory=src/main/resources -Dliquibase.verbose -pl database
```

#### 01-Create-Database.sql
Utilisation du goal maven liquibase:updateSQL qui génère un script sql basé sur le master passé en argument. Ecrit également un fichier databasechangelog.csv qui va tracer quels changeset auront été créées etc. Les scripts sont générés sur un nouveau docker à chaque fois donc ils sont neufs à chaque run de la CI.

```bash
mvn -Ptest liquibase:updateSQL -Dliquibase.url=offline:oracle -Dliquibase.changeLogFile=db/changelog/1.0.0/struct/tables/master.xml -Dliquibase.changeLogDirectory=src/main/resources -Dliquibase.verbose -pl database
```

#### 02-Create-Indexes.sql
Idem création des tables, mais ciblé sur le dossier des indexes.

```bash
mvn -Ptest liquibase:updateSQL -Dliquibase.url=offline:oracle -Dliquibase.changeLogFile=db/changelog/1.0.0/struct/indexes/master.xml -Dliquibase.changeLogDirectory=src/main/resources -Dliquibase.verbose -pl database
```

### Génération de la partie données
#### Données de la version (pour env de dev et tests)
Le script qui génère la structure s'occupe également de la génération des données de la version. Ces données sont expliquées plus haut.

Le script (pas forcément propre, mais ça marche pas mal), va boucler sur tous les dossiers se trouvant dans database/src/main/resources/db/changelog/1.0.0/data/. Donc tous les dossiers 00.referencs à 16.comac. On peut donc en ajouter d'autres sans avoir besoin de venir modifier le script.

Pour chaque dossier, il va tenter de générer un fichier SQL dont le nom est composé du numéro du dossier, puis "-Insert-", puis le nom du dossier (exemple "users"), et enfin ".sql".
Pour générer ce fichier, il va lancer la même commande que pour la création des tables et des indexes (updateSQL), sur un fichier master.xml qui doit obligatoirement se trouver dans le dossier.

Il va ensuite déplacer le fichier nouvellement créé dans les dossiers /oracle/...
```bash
find database/src/main/resources/db/changelog/1.0.0/data/* -type d | while IFS= read -r directoryPath; do
    directoryPath=${directoryPath#*/}
    directoryName=${directoryPath%%+(/)}
    directoryName=${directoryName##*/}

    numero=${directoryName%.*}
    name=${directoryName#*.}

    master="${directoryPath}/master.xml"
    nomFichier="$numero-Insert-$name.sql"

    mvn $MAVEN_CLI_OPTS -Ptest liquibase:updateSQL -Dliquibase.url=offline:oracle -Dliquibase.changeLogFile=$master -Dliquibase.verbose -pl database
    mv ./database/target/liquibase/migrate.sql "./oracle/1.0.0/datas/APS/${nomFichier}"
done
```

#### Données de prod (oneshot pour la première MEP)
Au vu des fonctionnalités du MVP, il n'est pas possible de créer des ouvrages et leurs politiques de surveillance depuis l'application. Nous avons donc fourni l'insertion des données initiales, par le même moyen que les données de tests pour avoir une garantie du fonctionnement.

Pour cela, respecter les mêmes normes que pour les données de tests et la même arborescence de dossiers, sous le dossier 'prod' se trouvant à la même hauteur que les versions.

Le script generateDataProd.sh est identique à celui qui génère les données de tests, à l'exception près qu'il ne génère pas la structure.
