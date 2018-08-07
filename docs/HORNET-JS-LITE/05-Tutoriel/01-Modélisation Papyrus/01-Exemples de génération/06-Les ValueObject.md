# Exemple de génération de nomenclature

![Exemple value object](../../sources/exemple_valueobject.png)

## Script de création de la base

createTablesPostgres.sql:

```sql
CREATE TABLE ma_classe(
	id integer NOT NULL,
	un_attribut_date_creat timestamp NOT NULL,
	un_attribut_date_update timestamp NOT NULL
);

ALTER TABLE ONLY ma_classe
	ADD CONSTRAINT ma_classe_pkey PRIMARY KEY (id);
```

## Sequelize Models

ma-classe-model.ts:

```javascript
import Sequelize = require("sequelize");

export const MaClasseModel: Sequelize.DefineAttributes = {
    id: {
        type: Sequelize.INTEGER,
        field: "id",
        allowNull: false,
        primaryKey: true,
    },
    unAttributDateCreat: {
        type: Sequelize.DATE,
        field: "un_attribut_date_creat",
        allowNull: false,
    },
    unAttributDateUpdate: {
        type: Sequelize.DATE,
        field: "un_attribut_date_update",
        allowNull: false,
    },
};
```

## Interface attributes

historique-attributes.ts:

```javascript
export interface HistoriqueAttributes {
    
    dateCreat?: string;
    dateUpdate?: string;

}
```

ma-classe-attributes.ts:

```javascript
import { HistoriqueAttributes } from "src/models/attributes/historique-attributes";

export interface MaClasseAttributes {
    
    id?: number;
    unAttributDateCreat?: string;
    unAttributDateUpdate?: string;

    unAttribut: HistoriqueAttributes;
    getUnAttribut(): HistoriqueAttributes;
    
}
```

## DTO

ma-classe-dto.ts:

```javascript
import Alias from "hornet-js-bean/src/decorators/Alias";
import Bean from "hornet-js-bean/src/decorators/Bean";
import Map from "hornet-js-bean/src/decorators/Map";


@Bean
export class MaClasseDTO {
    
    @Map()
    id: number;
    
    @Map()
    @Alias("unAttribut.dateCreat", "unAttributDateCreat")
    unAttributDateCreat: Date;
    
    @Map()
    @Alias("unAttribut.dateUpdate", "unAttributDateUpdate")
    unAttributDateUpdate: Date;
}
```

## Classes métier

historique-metier.ts:

```javascript
import Bean from "hornet-js-bean/src/decorators/Bean";
import Map from "hornet-js-bean/src/decorators/Map";


@Bean
export class HistoriqueMetier {
    
    @Map()
    dateCreat: Date;
    
    @Map()
    dateUpdate: Date;
    
}
```

ma-classe-metier.ts:

```javascript
import Bean from "hornet-js-bean/src/decorators/Bean";
import Map from "hornet-js-bean/src/decorators/Map";

import { HistoriqueMetier } from "src/models/metier/historique-metier";

@Bean
export class MaClasseMetier {
    
    @Map()
    id: number;
    
    @Map(HistoriqueMetier)
    unAttribut: HistoriqueMetier;
    
}
```