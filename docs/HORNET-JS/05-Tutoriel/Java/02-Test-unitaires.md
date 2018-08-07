# Test unitaire

## Gestion des tests

### Principes généraux

- Créer un dossier `src/test/java` (et si besoin `src/test/resources`) à partir de la racine du projet.
- Créer une classe de test par classe testée. La classe est suffixée par Test (exemple : `ClientServiceTest.java` pour la classe de test associée à `ClientService.java`).
- Créer une méthode par scénario de test envisagé (toujours prévoir au moins un cas normal et un cas d’erreur)
- En cas de correction d’anomalie, toujours créer un test unitaire reproduisant l’anomalie avant de la corriger.


### Exemple : test de service

```java

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(locations = {"/spring-appContext-test.xml"})
    public class PartenaireDAOTest {

        /** <code>partenaireDao</code> partenaireDao */
        @Autowired
        private transient PartenaireDAO partenaireDao;

        /**
         * Test de la requete testRetrieveDataList de PartenaireDao
         */
        @Test
        public void testRetrieveDataList() {

            final List<Partenaire> partenaires = this.partenaireDao.retrieveDataList();

            Assert.assertFalse(partenaires.isEmpty());
        }

        /**
         * Test de la requete SelectByCriteres de PartenaireDao
         */
        @Test
        public void testSelectByCritereStartDate() {

            final FormRecherchePartenaire criteres = new FormRecherchePartenaire();
            final Calendar cal = Calendar.getInstance();

            cal.set(2050, 11, 31);
            criteres.setStartDate(cal.getTime());
            List<Partenaire> partenaires = this.partenaireDao.selectByCriteres(criteres);
            Assert.assertTrue(partenaires.isEmpty());

            cal.set(2049, 11, 31);
            criteres.setStartDate(cal.getTime());
            partenaires = this.partenaireDao.selectByCriteres(criteres);
            Assert.assertEquals(1, partenaires.size());
        }

        /**
         * Test de la requete SelectByCriteres de PartenaireDao
         */
        @Test
        public void testSelectByCriteres() {

            final FormRecherchePartenaire criteres = new FormRecherchePartenaire();
            final Calendar cal = Calendar.getInstance();
            criteres.getPartenaire().setIsClient(Boolean.TRUE);
            criteres.setStartDate(cal.getTime());

            final List<Partenaire> partenaires = this.partenaireDao.selectByCriteres(criteres);

            Assert.assertFalse(partenaires.isEmpty());
        }
    }
```