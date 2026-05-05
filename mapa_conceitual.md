
projeto-dao-base/
├─ pom.xml
├─ README.md
└─ src/
   ├─ main/
   │  ├─ java/
   │  │  └─ br/escola/dao_base/
   │  │     ├─ app/
   │  │     │  └─ Main.java
   │  │     ├─ model/
   │  │     │  └─ (entidades do tema entram depois)
   │  │     ├─ dao/
   │  │     │  └─ (interfaces entram depois)
   │  │     ├─ dao/impl/
   │  │     │  └─ (implementações JDBC entram depois)
   │  │     └─ db/
   │  │        ├─ ConnectionFactory.java
   │  │        └─ DbException.java
   │  └─ resources/
   │     ├─ db.properties
   │     └─ sql/
   │        ├─ schema.sql
   │        └─ seed.sql
   └─ test/
      └─ java/ (opcional)
