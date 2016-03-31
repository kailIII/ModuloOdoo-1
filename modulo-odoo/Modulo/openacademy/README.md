Trabajo de como crear un Modulo en Odoo, hemos utilizado estos tutoriales:
la documentacion de:
https://www.odoo.com/documentation/8.0/howtos/backend.html



Después de borrar, pegar, editar por fin lo hemos conseguido.

Tutorial y documentacion sobre Odoo: https://www.odoo.com/documentation/8.0/howtos/backend.html

Github seguido para poder acabar el trabajo: https://github.com/damiannogueiras/moduloOdoo

####Creación del módulo de Odoo

Accedemos a Netbeans y creamos un proyecto llamado "Odoo" por ejemplo.

El siguiente paso sera crear una carpeta que se llame "openacademy", dentro del proyecto creado.

###Comenzamos

Los siguientes pasos se harán dentro de la carpeta "openacademy":

**1)**

    Creamos una carpeta llamada controllers y dentro de ella, dos ficheros, que contendrán lo siguiente:

**__init__.py**

```java
# -*- coding: utf-8 -*-

from . import controllers
```
**controllers.py**

```java

# -*- coding: utf-8 -*-
from openerp import http

# class Openacademy(http.Controller):
#     @http.route('/openacademy/openacademy/', auth='public')
#     def index(self, **kw):
#         return "Hola"

#     @http.route('/openacademy/openacademy/objects/', auth='public')
#     def list(self, **kw):
#         return http.request.render('openacademy.listing', {
#             'root': '/openacademy/openacademy',
#             'objects': http.request.env['openacademy.openacademy'].search([]),
#         })

#     @http.route('/openacademy/openacademy/objects/<model("openacademy.openacademy"):obj>/', auth='public')
#     def object(self, obj, **kw):
#         return http.request.render('openacademy.object', {
#             'object': obj
#         })

```

##Carpeta demo##

```java

<openerp>
    <data>
        <!--  -->
           <record id="course0" model="openacademy.course"> 
                <field name="name">Curso 0</field> 
                <field name="description">Curso Zero descripcion
             
             Puede tener mas de una linea
                </field> 
           </record>
            <record id="course1" model="openacademy.course"> 
                <field name="name">Curso 1</field> 
                <field name="description">
                    
                    Curso puede tener mas de una linea
             
             
                </field> 
           </record>
            <record id="course2" model="openacademy.course"> 
                <field name="name">Curso 2</field> 
                <field name="description">
                    
                    Curso puede tener dos lineas
             
             
                </field> 
           </record>

    </data>
</openerp>

```

##Carpeta models##

Contendrá dos ficheros, ambos .py, serán __init__.py y models.py

**__init__.py**

```java

# -*- coding: utf-8 -*-

from . import models

```

**models.py**

```java

# -*- coding: utf-8 -*-

from openerp import models, fields, api

class Course(models.Model):
    _name = 'openacademy.course'

    name = fields.Char(string="Titulo", required=True)
    description = fields.Text()
    responsible_id = fields.Many2one('res.users',
        ondelete='set null', string="Responsable", index=True)
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sesiones")

class Session(models.Model):
    _name = 'openacademy.session'

    name = fields.Char(required=True)
    start_date = fields.Date()
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor")
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Curso", required=True)
```

##Carpeta security##

Creamos un fichero llamado "ir.model.access.csv" que contendrá:

**ir.model.access.csv**

```java

id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_openacademy_openacademy,openacademy.openacademy,model_openacademy_openacademy,,1,0,0,0

```

##Carpeta views##

3 ficheros .xml formarán esta carpeta: "openacademy.xml" , "templates.xml" y "views.xml"

**openacademy.xml**

    Da estructura al apartado cursos 

```java

<?xml version="1.0" encoding="UTF-8"?>
<openerp>
    <data>
        <record model="ir.ui.view" id="course_form_view">
            <field name="name">course.form</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <form string="Course Form">
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="responsible_id"/>
                        </group>
                        <notebook>
                            <page string="Description">
                                <field name="description"/>
                            </page>
                            <page string="Sesiones">
                                <field name="session_ids">
                                    <tree string="Registered sessions">
                                        <field name="name"/>
                                        <field name="instructor_id"/>
                                    </tree>
                                </field>
                            </page>
                            <page string="Observaciones">
                                Esto es un ejemplo de notebooks
                            </page>
                        </notebook>
                    </sheet>
                </form>
            </field>
        </record>
        
        <!-- override the automatically generated list view for courses -->
        <record model="ir.ui.view" id="course_tree_view">
            <field name="name">course.tree</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <tree string="Course Tree">
                    <field name="name"/>
                    <field name="responsible_id"/>
                </tree>
            </field>
        </record>
        
        <record model="ir.ui.view" id="course_search_view">
            <field name="name">course.search</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <search>
                    <field name="name"/>
                    <field name="description"/>
                </search>
            </field>
        </record>
        
        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
            that is an action opening a view or a set of views
        -->
        <record model="ir.actions.act_window" id="course_list_action">
            <field name="name">Cursos</field>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
            <field name="help" type="html">
                <p class="oe_view_nocontent_create">Crea el primer curso
                </p>
            </field>
        </record>

        <!-- top level menu: no parent -->
        <menuitem id="main_openacademy_menu" name="Open Academy"/>
        <!-- A first level in the left side menu is needed
        before using action= attribute -->
        <menuitem id="openacademy_menu" name="Open Academy"
                  parent="main_openacademy_menu"/>
        <!-- the following menuitem should appear *after*
        its parent openacademy_menu and *after* its
        action course_list_action -->
        <menuitem id="courses_menu" name="Courses" parent="openacademy_menu"
                  action="course_list_action"/>
        <!-- Full id location:
        action="openacademy.course_list_action"
        It is not required when it is the same module -->
        
        <!-- session form view -->
        <record model="ir.ui.view" id="session_form_view">
            <field name="name">session.form</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <form string="Session Form">
                    <sheet>
                        <group>
                            <group string="General">
                                <field name="course_id"/>
                                <field name="name"/>
                                <field name="instructor_id"/>
                            </group>
                            <group string="Schedule">
                                <field name="start_date"/>
                                <field name="duration"/>
                                <field name="seats"/>
                            </group>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>
        
        <!-- session tree/list view -->
        <record model="ir.ui.view" id="session_tree_view">
            <field name="name">session.tree</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <tree string="Session Tree">
                    <field name="name"/>
                    <field name="course_id"/>
                </tree>
            </field>
        </record>
        
        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
        </record>
             
        <menuitem id="session_menu" name="Sessions"
                       parent="openacademy_menu"
                       action="session_list_action"/>
    </data>
</openerp>

```

**templates.xml**

```java

<openerp>
    <data>
        <!-- <template id="listing"> -->
        <!--   <ul> -->
        <!--     <li t-foreach="objects" t-as="object"> -->
        <!--       <a t-attf-href="#{ root }/objects/#{ object.id }"> -->
        <!--         <t t-esc="object.display_name"/> -->
        <!--       </a> -->
        <!--     </li> -->
        <!--   </ul> -->
        <!-- </template> -->
        <!-- <template id="object"> -->
        <!--   <h1><t t-esc="object.display_name"/></h1> -->
        <!--   <dl> -->
        <!--     <t t-foreach="object._fields" t-as="field"> -->
        <!--       <dt><t t-esc="field"/></dt> -->
        <!--       <dd><t t-esc="object[field]"/></dd> -->
        <!--     </t> -->
        <!--   </dl> -->
        <!-- </template> -->
    </data>
</openerp>

```

**views.xml**

```java

<openerp>
  <data>
    <!-- explicit list view definition -->
    <!--
    <record model="ir.ui.view" id="openacademy.list">
      <field name="name">openacademy list</field>
      <field name="model">openacademy.openacademy</field>
      <field name="arch" type="xml">
        <tree>
          <field name="name"/>
          <field name="value"/>
          <field name="value2"/>
        </tree>
      </field>
    </record>
    -->

    <!-- actions opening views on models -->
    <!--
    <record model="ir.actions.act_window" id="openacademy.action_window">
      <field name="name">openacademy window</field>
      <field name="res_model">openacademy.openacademy</field>
      <field name="view_mode">tree,form</field>
    </record>
    -->

    <!-- server action to the one above -->
    <!--
    <record model="ir.actions.server" id="openacademy.action_server">
      <field name="name">openacademy server</field>
      <field name="model_id" ref="model_openacademy_openacademy"/>
      <field name="code">
        action = {
          "type": "ir.actions.act_window",
          "view_mode": "tree,form",
          "res_model": self._name,
        }
      </field>
    </record>
    -->

    <!-- Top menu item -->
    <!--
    <menuitem name="openacademy" id="openacademy.menu_root"/>
    -->
    <!-- menu categories -->
    <!--
    <menuitem name="Menu 1" id="openacademy.menu_1" parent="openacademy.menu_root"/>
    <menuitem name="Menu 2" id="openacademy.menu_2" parent="openacademy.menu_root"/>
    -->
    <!-- actions -->
    <!--
    <menuitem name="List" id="openacademy.menu_1_list" parent="openacademy.menu_1"
              action="openacademy.action_window"/>
    <menuitem name="Server to list" id="openacademy" parent="openacademy.menu_2"
              action="openacademy.action_server"/>
    -->
  </data>
</openerp>

```

###Último paso###

Después de crear las carpetas con sus respectivos ficheros, crearemos un "__init__.py", "__openerp__.py" y "partner.py"

**__init__.py**

```java

# -*- coding: utf-8 -*-

from . import controllers
from . import models

```

**__openerp__.py**

```java

# -*- coding: utf-8 -*-
{
    'name': "openacademySXE",

    'summary': """
        Modulo para SXE""",

    'description': """
        Long description del modulo bla bla bla
    """,

    'author': "Diego Abal",
    'website': "Odoo",

    # Categories can be used to filter modules in modules listing
    # Check https://github.com/odoo/odoo/blob/master/openerp/addons/base/module/module_data.xml
    # for the full list
    'category': 'Uncategorized',
    'version': '4.0',

    # any module necessary for this one to work correctly
    'depends': ['base'],

    # always loaded
    'data': [
        #'security/security.xml',
        #'security/ir.model.access.csv',
        #'views/views.xml',
        #'views/templates.xml',
        'views/openacademy.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
        #'demo/demo.xml',
    ],
}

```

**partnet.py**

```java

# -*- coding: utf-8 -*-
from openerp import fields, models

class Partner(models.Model):
    _inherit = 'res.partner'

    # Add a new column to the res.partner model, by default partners are not
    # instructors
    instructor = fields.Boolean("Instructor", default=False)

    session_ids = fields.Many2many('openacademy.session',
        string="Attended Sessions", readonly=True)

```

####Espero que os haya servido de ayuda####