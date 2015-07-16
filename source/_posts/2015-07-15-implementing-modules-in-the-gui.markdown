---
layout: post
title: "Implementing modules in CPAC's GUI"
date: 2015-07-15 12:24:57 +0200
comments: true
categories: 
---



This week I have been trying to work with the migration of our modules to the GUI. For that, you will need to perform three main steps:

- Create the interface for your modules (2 steps)
   - Define your space in the main configuration window tree. (Name your module/submodules)
   - Define a window with configuration options (inputs and output of your workflow basically) for each one of your modules/submodules. The main window of the module usually is referred to an html file explaining what this module does and how does it work.
- Link the GUI with your workflows (1 step)
   - Link the configuration window of each of the submodules with the corresponding workflow through the pipeline module, allowing the execution of your workflows. 

## ** Create the interface for your modules **

#### ** STEP 1 **

The interface for the modules needs to be created. This is done by adding your module structure to the main configuration tree (a bit of planification is needed before deciding how the module structure is going to be organized). The first 2 steps should be done in parallel, I choosed to create the tree structure first, because I thought it was better a up to bottom approach.

First of all, you have to define your space in the [config_window](https://github.com/FCP-INDI/C-PAC/blob/master/CPAC/GUI/interface/windows/config_window.py) file, in the Class named 'Mybook'. 

There, you can find all the module names that will be listed in the main left of the pipeline configuration window. As an example, I have added some lines at the end of the Class:

{% codeblock lang:py %}
 	self.AddPage(page43, "CWAS", wx.ID_ANY)
        self.AddSubPage(page44, "CWAS Settings", wx.ID_ANY)

        self.AddPage(page45, "Group Analysis", wx.ID_ANY)
        self.AddSubPage(page46, "Group Analysis Settings", wx.ID_ANY)
        ### ADDED NEXT 3 LINES: 48 is my main module name. 49 and 50 submodules inside 48
        self.AddPage(page48, "Non Linear TS Analysis", wx.ID_ANY)
        self.AddSubPage(page49, "Information Theory", wx.ID_ANY)
        self.AddSubPage(page50, "Causality", wx.ID_ANY)
{% endcodeblock %}

It is also important to add the name of your GUI Classes in the import at the beginning of the file. In the second step, these Classes will be defined.

#### ** STEP 2 **

In this step the Classes that create your configuration pages must be created. Here, it will be defined a window with configuration options (inputs and output of your workflow basically) for each one of your modules/submodules. For this purpose, it is neccessary to create a new file in the ['pages' directory](https://github.com/FCP-INDI/C-PAC/tree/master/CPAC/GUI/interface/pages), other files can be used an example. 

The main window of the module (in our example the 48) is usually referred to an html file explaining what this module does and how does it work. If you don't have such file yet, you can leave it blank. For the rest of submodules, as CPAC's has its own framework built over [wx](http://www.wxpython.org/), we can choose between many specific buttons and data tpyes to add to our window:

{% codeblock lang:py %}
control = enum(CHOICE_BOX = 0, 
               TEXT_BOX = 1, 
               COMBO_BOX = 2,
               INT_CTRL = 3,
               FLOAT_CTRL = 4,
               DIR_COMBO_BOX = 5,
               CHECKLIST_BOX = 6,
               LISTBOX_COMBO = 7,
               TEXTBOX_COMBO = 8,
               CHECKBOX_GRID = 9)

dtype = enum(BOOL = 0,
             STR= 1,
             NUM= 2,
             LBOOL = 3,
             LSTR = 4,
             LNUM = 5,
             LOFL = 6,
             COMBO = 7,
             LDICT= 8 )  
{% endcodeblock %}

This is an example of how it can be a GUI frame for our toolbox:

{% codeblock lang:py %}
class InformationTheory(wx.ScrolledWindow):
    
    def __init__(self, parent, counter = 0):
        wx.ScrolledWindow.__init__(self, parent)        
        self.counter = counter

        self.page = GenericClass(self, "Information Theory Calculation Options")
        
        self.page.add(label="Run Information Theory Measures", 
                 control=control.CHOICE_BOX, 
                 name='runIT', 
                 type=dtype.LSTR, 
                 comment="Run Information Theory Measures", 
                 values=["Off","On"],
                 wkf_switch = True)
                 
        self.page.add(label="Voxelwise / ROI extraction", 
                 control=control.CHOICE_BOX, 
                 name='voxel_roi', 
                 type=dtype.LSTR, 
                 comment="Run Information Theory Measures voxelwise or after ROI timeseries 			 extraction", values=["Voxelwise","ROI","Voxelwise/ROI"],
                 wkf_switch = True)         
        
        self.page.add(label="fMRI image", 
                     control=control.COMBO_BOX, 
                     name='input_image', 
                     type=dtype.STR, 
                     comment="fMRI image for calculation")
       
        self.page.add(label="Parcellation Mask", 
                     control=control.COMBO_BOX, 
                     name='input_mask', 
                     type=dtype.STR, 
                     comment="Parcellation Mask if you want to calculate")

        self.page.add(label = "Measures:",
                      #control = control.CHECKLISTBOX_COMBO,
                      control = control.LISTBOX_COMBO,
                      name = "Measures",
                      type = dtype.LDICT,
                      values = ['Entropy', 'Conditional Entropy','Mutual Information','Transfer Entropy','Entropy Correlation Coefficient'],
                      comment = "Select which IT measures to apply:\n"\
                                "ent = Entropy\n"\
                                 "condent = Conditional Entropy\n"\
                                 "mi = Mutual Information\n"\
                                 "te = Transfer Entropy\n"\
                                 "ecc = Entropy Correlation Coefficient\n",
                     size = (300,120),
                     combo_type =1)
     
        self.page.add(label="Output Options ",
                      control=control.CHECKLIST_BOX,
                      name="measure_options",
                      type=dtype.LBOOL,
                      values=['CSV', 'NUMPY','NIFTI'],
                      comment="By default, results are written as NIFTI files. Additional output formats are as a .csv spreadsheet or a Numpy array.")

        self.page.set_sizer()
        parent.get_page_list().append(self)        
{% endcodeblock %}

It is important to update the __init__ file of the folder 'pages' to make the new file available and checking all the intermodule dependencies and imports. 
After doing these modifications, the modifications will be seen as long as the [MainUI](https://github.com/FCP-INDI/C-PAC/blob/master/CPAC/GUI/mainUI.py) file runs without problem. 

In the next figure, it can be seen how the example code is translated into the GUI:

{% img center /images/CPAC_GUI.png 800 800 'image' 'image' %}

After completing your configuration, a YAML file will be created with the information provided into the different inputs. 

## ** Link the GUI with your workflows **

#### ** STEP 3 **

This is probably the most important step along with having a good planification of the needs of the module. The objective here is to link the configuration window of each of the submodules with the corresponding workflow through the pipeline module and specifically the [cpac_pipeline](https://github.com/FCP-INDI/C-PAC/blob/master/CPAC/pipeline/cpac_pipeline.py) file, allowing the execution of your workflows. 

First of all, it is needed to import the workflows into the file. After that, and having as a reference other workflow insertions, you can take the input data saved in the previous step from a [Configuration object](https://github.com/FCP-INDI/C-PAC/blob/master/CPAC/utils/configuration.py) called 'c'. This ‘c’ variable is an abstract representation used to store the options for the pipeline configuration YAML. The use of the YAML file, allows users to load preconfigured files easily. 
The YAML was generated by the GUI in the previous step and it is reloaded back in by [cpac_runner](https://github.com/FCP-INDI/C-PAC/blob/afbc30e28e4c282847920119e922953690835d36/CPAC/pipeline/cpac_runner.py#L381)  as a Configuration object (‘c’).  Finally, _cpac_runner.py_ passes the Configuration object to _cpac_pipeline.py_ as an argument.

There is the need of checking if the inputs are correct (otherwise, raise an error message), create the workflow and give to it the inputs extracted from 'c'. 
_cpac_pipeline.py_ is where the Nipype workflow object for each pipeline strategy is constructed via flow control. We will want to add a code block similar to [this](https://github.com/FCP-INDI/C-PAC/blob/master/CPAC/pipeline/cpac_pipeline.py#L3028-L3197)  in order to add the nonlinear time-series node to these workflows.  The functions will be encapsulated within nodes and connected to a workflow.


Short example of how to manage the inputs:

{% codeblock lang:py %}
    '''
    Inserting NLTSA
    Workflow
    '''
    
    new_strat_list = []
    num_strat = 0

    if 1 in c.runIT:
        for strat in strat_list:
            if 'mi' in c.MEASURES:

            pipeline = create_mi()
            pipeline.inputs.inputspec.in_file = c.input_image
            pipeline.inputs.inputspec.mask_file = c.input_mask
            pipeline.run()

            num_strat += 1
    strat_list += new_strat_list
{% endcodeblock %}


After completing this last step, the new module of CPAC should be working. 

