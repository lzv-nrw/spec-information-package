# Information Package Spezifikation

## 1. Allgemeines

Das vorliegende Dokument beschreibt den Aufbau eines Information Packages (IP), mithilfe dessen eine Intellektuelle Einheit (IE) über die Submission Application DCM dem Service LZV.nrw und damit der Langzeitverfügbarkeit zugeführt wird.

Der Aufbau basiert auf dem BagIt File Packaging Format (V1.0) der Library of Congress (RFC 8493)[^1].

Des weiteren gelten folgende generelle Festlegungen.

| **Merkmal** | **Festlegung** |
|-|-|
| **Granularität** | Jedes IP enthält eine IE[^2]. |
| **Ordnername eines IP** | IPs werden als einzelne Verzeichnisse repräsentiert, deren Namen nicht ausgewertet wird, jedoch eindeutig sein sollten (z.B. Zeitstempel). |
| **Datei- und Ordnernamen** | Datei- und Ordnernamen sollten sich nicht nur in der Groß- / Kleinschreibung unterscheiden, damit Dateien und Ordner nicht unbeabsichtigt überschrieben werden. |
| **Archivdateien** | IPs sollten keine gepackten Dateien enthalten, da andernfalls keine Formaterkennung und Validierung durchgeführt werden können. In der weiteren Verarbeitung können dadurch Warnungen entstehen. |
| **Versionierung und Updates** | Es ist vorerst nicht möglich, mehrere Versionen einer Repräsentation in einem IP abzulegen. <br><br> Es ist vorerst nicht möglich, eine bereits abgelieferte IE via der Spezifikation entsprechendem IP zu updaten. <br><br> Es ist vorerst nicht möglich, eine migrierte IE in das Quellsystem automatisiert zurückzuspielen. |
| **IPs ohne Payload** | IPs ohne Payload werden nicht akzeptiert. Für Metadata-only IPs kann als Workaround eine leere Datei `data/preservation_master/.keep` angelegt werden, so dass das IP gültig wird. |

[^1]: https://datatracker.ietf.org/doc/rfc8493/
[^2]: Eine Intellektuelle Einheit ist eine Menge von Dateien, die gemeinsam verwaltet und langzeitverfügbar gehalten werden soll.

## 2. Metadaten

Metadaten werden in Textdateien erfasst. Dabei muss UTF-8 ohne BOM als Zeichenkodierung verwendet werden.

### 2.1 bag-info.txt

Um die Erstellung der IPs einfach zu halten, werden möglichst viele Informationen als Key-Value-Paar in der bag-info.txt festgehalten.

| **Label** | **Wertebereich** | **Beschreibung** | **Verwendungszweck** | **Beispiel** | **Kardinalität** | **Anmerkungen** |
|--|--|--|--|--|--|--|
| **Bag-Software-Agent** | Applikationsname vVersionsnummer | Bag Builder Applikation | Zuordenbarkeit | Bag Builder v1.2.3 | 0-1 | |
| **Payload-Oxum[^3]** | OctetCount.StreamCount | Größe des Payloads | Validierung | 388743.4 | 1 | Wird automatisch von python lib der LOC erstellt; bietet vor der Prüfsummenerstellung eine schnelle Rückmeldung, ob ein Bag unvollständig ist |
| **Source-Organization[^3]** | string | GND URI | Zuordenbarkeit | https://d-nb.info/gnd/5091030-9 | 1 | Bag-Erstellung muss nicht durch DCM erfolgen, solange das Bag via GND ID zugeordnet werden kann; <br> perspektivisch weitere Identifier möglich, z.B. ISIL URI (info:isil/DE-MUS-149328) |
| **External-Identifier[^3]** | string | Identifier der IE im Quellsystem | Identifikation, Update im Archiv | 3192@9361250c-dd0d-4a76-a2c7-c18de46502a6 | 1 | In den meisten Fällen wird die ID durch das Quellsystem vergeben, ansonsten durch die Institution. Letztere ist auch dafür verantwortlich, dass die Zuordenbarkeit erhalten bleibt. |
| **Origin-System-Identifier** | string | Quellsystem-Identifier | Identifikation, Nachweisbarkeit, Update im Archiv | | 1 | Gemeinsam mit External-Identifier zur Identifikation einer IE im Archiv durch DCM <br><br> Identifier bezieht sich auf genau eine Instanz des Quellsystems. |
| **DC-Creator** | string | dc:creator | Auffindbarkeit in Archiv | Max Mustermann | 0-n | Creator bzw. Autor ist nicht immer vorhanden. Bei Vorhandensein sollte er eingetragen werden. |
| **DC-Title** | string | dc:title | Auffindbarkeit in Archiv, Bezeichner | Macht der Neuen Medien? | 1-n | |
| **DC-Terms-Identifier** | string | dcterms:identifier | Auffindbarkeit in Archiv, Identifikation im Archiv, Mindestanforderung hbz/Rosetta | doi: 10.1007/s11616-005-0142-4 | 0-n | Wenn möglich global und standardisiert, z.B. URN, DOI; <br><br> Falls 0: Es wird automatisch ein Identifier aus Source-Organization, Origin-System-Identifier und External-Identifier gebildet und eingesetzt; dieser muss global eindeutig sein |
| **DC-Rights** | string | dc:rights | Rechte, Mindestanforderung hbz/Rosetta | Copyrighted | 1-n | Einziges verpflichtendes Feld für Rechte-Metadaten; kann frei befüllt werden. <br> Bei Nutzung von DC-Terms-Rights und/oder DC-Terms-License wird folgendes kontrolliertes Vokabular empfohlen: <ul><li>Public Domain</li><li>Copyrighted</li><li>Unknown</li></ul> Die genannten Felder können ergänzend zur Präzisierung verwendet werden. |
| **DC-Terms-Rights** | string | dcterms:rights | Rechte | https://rightsstatements.org/vocab/InC/1.0/ | 0-1 | |
| **DC-Terms-License** | string | dcterms:license | Rechte | https://creativecommons.org/licenses/by/4.0/ | 0-1 | |
| **DC-Terms-Access-Rights** | string | dcterms:accessRights | Rechte | info:eu-repo/semantics/openAccess | 0-1 | |
| **Embargo-Enddate** | YYYY-MM-DD | Enddatum des Embargos | Rechte | 2024-01-01 | 0-1 | Nur relevant, wenn entsprechendes Zugriffsrecht mit Embargo in DC-Terms-Access-Rights verwendet wird |
| **DC-Terms-Rights-Holder** | string | dcterms:rightsHolder | Rechte |https://orcid.org/0000-0003-2194-6754 | 0-1 | |
| **BagIt-Profile-Identifier** | URL | BagIt Profile Version | DCM | https://github.com/lzv-nrw/spec-information-package/raw/refs/tags/0.6.2/profiles/lzvnrw_bagit_profile.json | 1 | Referenz zu BagIt-Profile |
| **Bagging-DateTime** | YYYY-MM-DDThh:mm:ssTZD | Zeitpunkt der Erstellung | Nachweisbarkeit | 2023-04-03T13:37:00+02:00 | 1 | ISO 8601, Bagging-Date zu ungenau |
| **Preservation-Level** | string | Preservation Level | Erhaltungsplanung | Logical | 0-1 | Kontrolliertes Vokabular: <ul><li>Bitstream</li><li>Logical</li><li>Semantic</li></ul> |

[^3]: Reserved Field aus BagIt-Spezifikation der LOC

### 2.2 Metadaten Dateien

Wo nötig, werden zusätzliche Metadaten in separaten Dateien im meta Ordner abgelegt.

| **Datei** | **Format** | **Beschreibung** | **Verwendungszweck** | **Kardinalität** | **Anmerkungen** |
|-|-|-|-|-|-|
| **dc.xml** | DC | Dublin Core Metadaten, die über die Felder in der bag-info.txt hinausgehen | LZV von Metadaten, Recherchemöglichkeit in Archivsystem erweitern | 0-1 | Die bag-info.txt behält den Vorrang. In der dc.xml sollen nur darüber hinausgehende dc Metadaten festgehalten werden. Dopplungen werden ignoriert. |
| **significant_properties.xml** | PREMIS | Signifikante Eigenschaften | Migration (LZV) | 0-1 | |
| **source_metadata.xml** | durch Quellsystem vorgegeben | Metadaten des Quellsystems | Beschreibung der IE | 0-1 | Textrepräsentation der Quellmetadaten |
| **structure_metadata.xml** | noch festzulegen | Strukturinformationen zu Repräsentation(en) | Vollständige Erhaltung | 0-1 |
| **events.xml** | PREMIS | Prozessinformationen | Nachvollziehbarkeit | 0-1 |

#### 2.2.1 significant_properties.xml

Signifikanten Eigenschaften werden auf IE-Ebene festgehalten und in fünf Kategorien unterteilt. Jede Kategorie kann optional angegeben und mit einem Freitext beschrieben werden:

- Content
- Context
- Appearance
- Behavior
- Structure

Vollständiges Beispiel:
```XML
<premis:premis xmlns:premis="http://www.loc.gov/premis/v3" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.loc.gov/premis/v3 https://www.loc.gov/standards/premis/premis.xsd" version="3.0">
	<premis:object xsi:type="premis:intellectualEntity">
		<!-- Identifier points to the bag's payload directory that contains all representation subdirectories of the IE -->
		<premis:objectIdentifier>
			<premis:objectIdentifierType>Relative path</premis:objectIdentifierType>
			<premis:objectIdentifierValue>../data/</premis:objectIdentifierValue>
		</premis:objectIdentifier>
		<!-- Significant property: CONTENT -->
		<premis:significantProperties>
			<premis:significantPropertiesType>content</premis:significantPropertiesType>
			<premis:significantPropertiesValue>The full text content must remain unchanged and machine-readable.</premis:significantPropertiesValue>
		</premis:significantProperties>
		<!-- Significant property: CONTEXT -->
		<premis:significantProperties>
			<premis:significantPropertiesType>context</premis:significantPropertiesType>
			<premis:significantPropertiesValue>Metadata about the creation date and original author must be preserved.</premis:significantPropertiesValue>
		</premis:significantProperties>
		<!-- Significant property: APPEARANCE -->
		<premis:significantProperties>
			<premis:significantPropertiesType>appearance</premis:significantPropertiesType>
			<premis:significantPropertiesValue>Original layout, including headings and paragraph breaks, must be preserved for human readability.</premis:significantPropertiesValue>
		</premis:significantProperties>
		<!-- Significant property: BEHAVIOR -->
		<premis:significantProperties>
			<premis:significantPropertiesType>behavior</premis:significantPropertiesType>
			<premis:significantPropertiesValue>Hyperlinks embedded in the document must remain functional and allow navigation to linked resources.</premis:significantPropertiesValue>
		</premis:significantProperties>
		<!-- Significant property: STRUCTURE -->
		<premis:significantProperties>
			<premis:significantPropertiesType>structure</premis:significantPropertiesType>
			<premis:significantPropertiesValue>The section hierarchy and internal document organization must be maintained.</premis:significantPropertiesValue>
		</premis:significantProperties>
	</premis:object>
</premis:premis>
```

## 3. Bag-Struktur

![](./assets/media/bag_structure_diagram.svg)

## 4. BagIt-Profil (JSON)

Damit Bag produzierende und Bag konsumierende Systeme sich auf eine BagIt Struktur verständigen können, wird ein BagIt Profil eingesetzt. Es handelt sich hierbei um eine separate JSON Datei, welche die festgelegten Anpassungen beschreibt und [hier](https://bagit-profiles.github.io/bagit-profiles-specification/) spezifiziert ist. In der Sektion „Bag-Info" sind in den „description" Feldern reguläre Ausdrücke hinterlegt, die zur Formatvalidierung von Values der bag-info.txt benötigt werden.
