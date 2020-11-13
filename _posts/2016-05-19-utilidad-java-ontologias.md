---
layout: post
title: "Loading ontologies with JAVA (old code 2012)"
categories: 
    - "Java"
    - "Misc"
---

Hace tiempo que no uso Java. Hoy me he encontrado este código para maninpular ontologías. Que tiempos! Lo dejo por aquí:

A code to read ontologies using a reasoner. It is a very old code (2012).

## My recipe

```java

import com.hp.hpl.jena.ontology.OntDocumentManager;
import com.hp.hpl.jena.ontology.OntModel;
import com.hp.hpl.jena.ontology.OntModelSpec;
import com.hp.hpl.jena.query.*;
import com.hp.hpl.jena.rdf.model.ModelFactory;
import org.mindswap.pellet.jena.PelletReasonerFactory;

public class testQuery {

	/**
	 *  URI donde está ubicada la ontología
	 */
    static String URILocalFile = "file:///Users/isaac/ontologies/Animals.owl";
    static String URIExternal1 = "http://acsic.uib.es/isaaclera/ontology/Animals.owl";

    //EL NameSpace NS se puede obtener a partir de la ontología pero yo por temas de eficiencia se lo indico
    //El NS no tiene porque coincidir con el URI
    private static String NS = "http://acsic.uib.es/Animals.owl#";
//    private static String NS;

    //La ontologia cargada por el razonador
	private static String LoadURI = URIExternal1;


    private static OntModel model;

	
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		//Se carga la ontología con el modelo de razonador índicado
        model = getReasonerModelTest(LoadURI);

        //Se imprime la ontología por System.out en sintaxis indicada
		//model.write(System.out,"RDF/XML");

        //Definción del query
		String prefixes = 
			"PREFIX owl:  <http://www.w3.org/2002/07/owl#> " +
			"PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#> "+
			"PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> "+
			"PREFIX base: <"+NS+">";

		//QUERIES:
		//Listado de clases
		String query1 =
			"SELECT ?algo "
			  + " WHERE { " 
			    + " ?algo rdf:type owl:Class .  "
			  	+ " }";

        //Que cosas se come nemo
		String query2 = " Select ?x { " +
				" base:Charles base:come ?x ." +
				" }";

        //Charles se come a Nemo
        String query3 = " ASK { " +
                " base:Charles base:come base:Nemo ." +
                " }";
		/**
		 * Change your Query HERE!
         *
		 */
		String testQuery = query2;




		
		Query query = QueryFactory.create(prefixes+testQuery);
		QueryExecution qe = QueryExecutionFactory.create(query, model);
		
		if (testQuery.contains("ASK")){
			boolean result = qe.execAsk() ;
			System.out.println("\t Result : "+result);
		}else{
			ResultSet results = qe.execSelect();
	
			//¿Como utilizar los resultados?
			//A - Mostrados directamente por pantalla
			ResultSetFormatter.out(System.out, results, query);
			
			//B - a nivel de programación
//			for (;results.hasNext();){
//				QuerySolution soln = results.nextSolution() ;
//				//siendo X la variable
//				String cls = soln.get("x").asNode().getLocalName();
//				
//			}
		}
		
		qe.close();
		
	}
	
	public static OntModel getReasonerModelTest(String url) {
		OntDocumentManager mgr = new OntDocumentManager();
		mgr.setProcessImports(true);
		
		//Razonador de Jena
		//OntModelSpec s = new OntModelSpec(OntModelSpec.RDFS_MEM_RDFS_INF);
		
		//Razonador Pellet
		OntModelSpec s = new OntModelSpec(PelletReasonerFactory.THE_SPEC);
		 
		s.setDocumentManager( mgr );
		OntModel model = ModelFactory.createOntologyModel( s );
		
		System.out.println("\t Reading...");
		model.read(url);
		System.out.println("\t [ok]");
		
		// load the model to the reasoner
		System.out.println("\t Preparing...");
		model.prepare();
		System.out.println("\t [ok]");

        //Otro razonador factible
//		System.out.println("Classifying...");
//		((PelletInfGraph) model.getGraph()).getKB().classify();
//		System.out.println("[done]");
		
		System.out.println("\t NS = "+model.getNsPrefixURI(""));
		if (null==model.getNsPrefixURI("")){
			System.out.println("\t\t NS ERROR "); 
		}
		if (NS.length()<3) NS = model.getNsPrefixURI("");
		
		return model;
	}

}

```
