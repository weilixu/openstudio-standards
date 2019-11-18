# How to create a vintage

0. Create a folder with the name of the new vintage that you are creating.. It must be upper case and start with a letter and no special charecters.  
1. Create a folder called data within this new folder. Create an empty file called 'empty.txt' here
1. Copy the structure from an existing class that inherits from NECB2011 in the current folder. For example the contents of necb_2015 could be a staring point. 
2. Rename the class to the vintage that you wish.
3. Add the path of this new file to the openstudio header file here:
```
openstudio-standards/lib/openstudio-standards.rb
```
4. If you wish to reuse any aspects of the other vintages in your new vintage you will need to over-ride the *model_apply_standard* method in your child class. For example, if you wish to use the NECB2011 spacetypes you will need to do something like this. Note: The Standards.build invokes methods from other vintages. In this case the NECB2011. The other methods witout this will use the current class' vintage code and data. 
```ruby
  def model_apply_standard(model:,
                           epw_file:,
                           sizing_run_dir: Dir.pwd,
                           primary_heating_fuel: 'DefaultFuel')
    apply_weather_data(model: model, epw_file: epw_file)
    Standards.build("NECB2011").apply_loads(model: model)
    apply_envelope( model: model)
    apply_auto_zoning(model: model, sizing_run_dir: sizing_run_dir)
    apply_systems(model: model, primary_heating_fuel: primary_heating_fuel, sizing_run_dir: sizing_run_dir)
    apply_standard_efficiencies(model: model, sizing_run_dir: sizing_run_dir)
    model = apply_loop_pump_power(model: model, sizing_run_dir: sizing_run_dir)
    return model
  end
```
5. You will need to add your vintage to the space_type_vintage_list array contained in necb_2011.rb
```ruby
  #this method will determine the vintage of NECB spacetypes the model contains. It will return nil if it can't
  # determine it.
  def determine_spacetype_vintage(model)
    #this code is the list of available vintages
    space_type_vintage_list = ['NECB2011', 'NECB2015', 'NECB2017', 'BTAPPRE1980']
    #this reorders the list to do the current class first.
    space_type_vintage_list.insert(0, space_type_vintage_list.delete(self.class.name))
```

