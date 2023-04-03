# SchoolJava

#SchoolEar

C'est le projet à deployer il utilise les deux autres commme dependance.

#SchoolEJB

On a dans EJBmodule deux packages:

-Le package pour entities: on a Compte pour l'authentification, Courses ,Student ,Inscription,Year
Ils sont annotés avec @Entity 


-Le package pour DAO: Les interfaces et leurs implementations pour chaque entité et on a bien sûr une interface générique et son implementation qui regroupe les methodes
que les autres interfaces ont en commun.
Les interfaces sont annotées avec @Local et les implementations de ces interfaces avec @Stateless

#SchoolWeb

-Nous avons les controllers dans le package com.groupeisi.controllers:
-loginServlet: gère l'authentification, la création de la session et dirige vers la page d'accueil.
-logoutServlet: récupère la session, la détruit et renvoie dans la page de connexion
-StudentServlet: gère la l'ajout, suppression, modification des infos et l'affichage (de tous les etudiants ou un etudiant) d'etudiant.
il utilise l'interface Istudent dans ejb comme tel:
     @EJB
	private IStudent istudent ;
    
    recuperation d'un seul etudiant :
                 Student student = istudent.get(id,students);
	        	 request.setAttribute("student", student);
                 
   recuperation de tous les etudiants en utilisant la pagination :
        String paged = request.getParameter("page");
		int pageid=1;
        int total = 5;
        Student students = new Student();
        int limit = istudent.list(students).size();
        int pages = limit/total;
        int page=1;
        if(limit%total>0) {
        	pages=pages+1;
        }
		if(paged!=null) {
			pageid = Integer.parseInt(paged);
			 page = Integer.parseInt(paged);
		}
		
		
		if(pageid==1) {
			
		}else {
			pageid=pageid-1;
			pageid=pageid*total+1;
		
		}
   
                 Student student = new Student();
               
                List<Student> listStudent = istudent.liste(student,pageid,total);
              
                request.setAttribute("listStudent", listStudent);
                request.setAttribute("page", page);
                request.setAttribute("pages", pages);
                this.getServletContext().getRequestDispatcher("/Student/list.jsp").forward(request, response);
                
 -Suppression:
                int id =Integer.parseInt(idDel);
	        	
	        	int result = istudent.delete(id,students);
 -Modification des infos:
        String date = request.getParameter("date");
		
		 DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
		
		 LocalDate birthdate = LocalDate.parse(date, formatter);
			String idS = request.getParameter("id");
			if(idS!=null) {
				int id =Integer.parseInt(idS);
				    String phone = request.getParameter("phone");
				    String firstName = request.getParameter("firstName");
				    String lastName = request.getParameter("lastName");
				    Student student = new Student();
				    student.setId(id);
			        student.setFirstName(firstName);
			        student.setLastName(lastName);
			        student.setPhone(phone);
			        student.setBirthdate(birthdate);
			        if(istudent.update(student)==1) {
			        	 request.setAttribute("success", "Modification reussi");
		       this.getServletContext().getRequestDispatcher("/Student/addStudent.jsp").forward(request, response);
			        	
			        }
			}
            
  -Ajout:
  
               if(idS==null) {
			   String phone = request.getParameter("phone");
			   String firstName = request.getParameter("firstName");
			   String lastName = request.getParameter("lastName");
			    Student student = new Student();
		        student.setFirstName(firstName);
		        student.setLastName(lastName);
		        student.setPhone(phone);
		        student.setBirthdate(birthdate);
		        if(istudent.add(student)==1) {
		        	 request.setAttribute("success", "Enregistrement reussi");
	       this.getServletContext().getRequestDispatcher("/Student/addStudent.jsp").forward(request, response);
		        	
		        }
			
			}
		
 
 
   Tous les autres ont le même comportement sauf inscription qui pendant l'inscription et la modification doit avoir les objet Year, Student, Courses associés.
   
   
   Et enfin on a les vues dans webapp:
   
   Exemple d'une vue d'affichage:    
             <c:forEach var="stud" items="${listStudent}">
                <tr >
                 <th scope="row">
                          <div class="form-check">
                            <input class="form-check-input" type="checkbox" value="" id="qq" />
                          </div>
                 </th>
                    <td><c:out value="${stud.firstName}" /></td>
                    <td><c:out value="${stud.lastName}" /></td>
                    <td><c:out value="${stud.birthdate}" /></td>
                     <td><c:out value="${stud.phone}" /></td>
                   
                   <td>
                          <a type="button" href="student?idD=<c:out value="${stud.id}" />" class="btn btn-danger btn-sm px-3">
                       Delete
                          
                          </a>
                         
                           <a type="button" href="student?idM=<c:out value="${stud.id}" />" class="btn btn-info btn-sm px-3">
                           Plus ...
                          </a>
                          
                           <a type="button" href="student?idP=<c:out value="${stud.id}" />" class="btn btn-primary btn-sm px-3">
                            Update
                          </a>
                        </td>
                   

                </tr>
            </c:forEach>
                  
                    </tbody>
                  </table>
           <nav aria-label="Navigation for students">
        <ul class="pagination">
            <c:if test="${page != 1}">
                <li class="page-item"><a class="page-link"
                    href="student?page=${ page - 1 }">Previous</a>
                </li>
            </c:if>

            <c:forEach begin="1" end="${pages}" var="i">
                <c:choose>
                    <c:when test="${ page == i}">
                        <li class="page-item active"><a class="page-link">
                                ${i} <span class="sr-only">(current)</span></a>
                        </li>
                    </c:when>
                    <c:otherwise>
                        <li class="page-item"><a class="page-link"
                            href="student?page=${i}">${i}</a>
                        </li>
                    </c:otherwise>
                </c:choose>
            </c:forEach>

            <c:if test="${page lt pages}">
                <li class="page-item"><a class="page-link"
                    href="student?page=${page+1}">Next</a>
                </li>
            </c:if>
        </ul>
    </nav>
             
             
             
Exemple d'un formulaire:

     <form method="post" action="student" class="m-5">
                 
                   <% if(request.getAttribute("success") != null){%>
                <div class="row form-group">
                     <hr>
                    <div class="col-md-6 ">
                        <span class='text-success '><%= request.getAttribute("success") %></span>
                    </div>
                     <hr>
                </div>
                    <%}%> 
                     <% if(request.getAttribute("student") == null){%>
                
                   
                  <table class="table table-borderless mb-0">
                    <thead>
                      <tr>
                        
                        <th scope="col"> 
                        <div class="form-group">
                          <label>FirstName :</label>
                            <input class="form-input" type="text"  id="firstName" name="firstName" />
                          </div>
                          </th>
                         
                        <th scope="col">
                         <div class="form-group">
                          <label>LastName  :</label>
                            <input class="form-input" type="text"  id="lastName" name="lastName" />
                          </div>
                          </th>
                           </tr>
                          <tr>
                        <th scope="col">
                         <div class="form-group">
                          <label>BirthDate :</label>
                            <input class="form-input" type="date"   id="date"  name="date" />
                          </div>
                          </th>
                          
                          <th scope="col">
                         <div class="form-group">
                          <label>Phone Number :</label>
                            <input class="form-input" type="text"  id="phone" name="phone" />
                          </div>
                          </th>
                       
                      </tr>
                      
                     
                        
                        
                    </thead>
                    <tbody>
                     
                      
            
                      
                    </tbody>
                  </table>
                  <div class="m-5 "> <button type="submit" class="btn btn-sm btn-success px-5">Enregistrer</button></div>
                   <%}%> 
                   
                    <% if(request.getAttribute("success") != null){%>
                <div class="row form-group">
                     <hr>
                    <div class="col-md-6 ">
                        <span class='text-success '><%= request.getAttribute("success") %></span>
                    </div>
                     <hr>
                </div>
                    <%}%> 
                     <% if((request.getAttribute("student") != null) && (request.getAttribute("detail") == null ) ){%>
                
                   
                  <table class="table table-borderless mb-0">
                    <thead>
                      <tr>
                        
                        <th scope="col"> 
                          <input class="" type="text" hidden value="<c:out value="${student.id}" />"  id="id" name="id" />
                        
                        <div class="form-group">
                          <label>FirstName :</label>
                            <input class="form-input" type="text" value="<c:out value="${student.firstName}" />"  id="firstName" name="firstName" />
                          </div>
                          </th>
                         
                        <th scope="col">
                         <div class="form-group">
                          <label>LastName  :</label>
                            <input class="form-input" type="text" value="<c:out value="${student.lastName}" />" id="lastName" name="lastName" />
                          </div>
                          </th>
                           </tr>
                          <tr>
                        <th scope="col">
                         <div class="form-group">
                          <label>BirthDate : </label>
                           
                          </div>
                           <input class="form-input" type="date" value="<c:out value="${student.birthdate}" />"   id="date"  name="date" />
                          </th>
                          
                          <th scope="col">
                         <div class="form-group">
                          <label>Phone Number :</label>
                            <input class="form-input" type="text" value="<c:out value="${student.phone}" />"  id="phone" name="phone" />
                          </div>
                          </th>
                       
                      </tr>
                      
                     
                        
                        
                    </thead>
                    <tbody>
                     
                      
            
                      
                    </tbody>
                  </table>
                  <div class="m-5 "> <button type="submit" class="btn btn-sm btn-success px-5">Enregistrer</button></div>
                   <%}%> 
                   
                    <% if( (request.getAttribute("detail") != null) && (request.getAttribute("student") != null ) ){%>
                
                   
                  <table class="table table-borderless mb-0">
                    <thead>
                      <tr>
                        
                        <th scope="col"> 
                          <input class="" type="text" hidden value="<c:out value="${student.id}" />"  id="id" name="id" />
                        
                        <div class="form-group">
                          <label>FirstName :</label>
                            <input class="form-input" type="text" value="<c:out value="${student.firstName}" />"  id="firstName" name="firstName" />
                          </div>
                          </th>
                         
                        <th scope="col">
                         <div class="form-group">
                          <label>LastName  :</label>
                            <input class="form-input" type="text" value="<c:out value="${student.lastName}" />" id="lastName" name="lastName" />
                          </div>
                          </th>
                           </tr>
                          <tr>
                        <th scope="col">
                         <div class="form-group">
                          <label>BirthDate : </label>
                         <input class="form-input" type="date" value="<c:out value="${student.birthdate}" />"  id="date" name="date" />
                           
                          </div>
                          
                          </th>
                          
                          <th scope="col">
                         <div class="form-group">
                          <label>Téléphone :</label>
                            <input class="form-input" type="text" value="<c:out value="${student.phone}" />"  id="phone" name="phone" />
                          </div>
                          </th>
                       
                      </tr>
                      
                      <tr> 
                        <th> <label>Insriptions</label></th>
                         </tr>
                         <tr>
                          <c:forEach var="inscr" items="${student.inscriptions}">
                         
                           <th scope="col">
                         <div class="form-group">
                          <label>Details :</label>
                            <input class="form-input" type="text" value="<c:out value="${inscr.details}" />"  id="phone" name="phone" />
                             </div>
                          </th>
                          
                          <th scope="col">
                         <div class="form-group">
                          <label>Année: </label>
                            <input class="form-input" type="text" value="<c:out value="${inscr.year.name}" />"  id="phone" name="phone" />
                         </div>
                         
                          </th>
                          </tr>
                         </c:forEach>
                         
                     
                      <tr>
                      <th> 
                      <a type="button" href="inscription?param=add" class="btn btn-sm btn-success px-5">Inscrire</a>
                      <br></th>
                       <th scope="col">
                        
                       <a type="button" href="student" class="btn btn-sm btn-danger px-5">Retour</a>
                         </th>
                        </tr>
                    </thead>
                    <tbody>
                     
                      
            
                      
                    </tbody>
                  </table>
                 
                   <%}%> 
                   
                   
                   
                  </form>
                  
               Merci d'avoir prie le temps de regarder tout se bla bla!!!
   
   
    
    

