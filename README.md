xUnique
=======

**xUnique**, is a Python script to regenerate `project.pbxproj`, a.k.a the Xcode project file, and make it unique and same on any machine.
 
As you may know, the [`UUID`](http://www.ietf.org/rfc/rfc4122.txt) generated by Xcode in the file is not unique for the same added file( or other entries like groups,build phases,etc.) on different machines, which makes it a developer's nightmare to merge and resolve conflicts in `project.pbxproj`.

**xUnique** convert all the 96bits `UUID`(24 hex chars) to MD5 hex digest(32 hex chars)

### How it works
----------------
1. convert `project.pbxproj` to JSON format
2. Iterate all `objects` in JSON and give every UUID an absolute path, and create a new UUID using MD5 hex digest of the path
	* All elements in this json object is actually connected as a tree
	* We give a path attribute to every node of the tree using its unique attribute; this path is the absolute path to the root node, 
	* Apply MD5 hex digest to the path for the node
3. Replace all old UUIDs with the MD5 hex digest and also remove unused UUIDs if any
4. Sort project file using the modified [`sort-Xcode-project-file`](https://github.com/WebKit/webkit/blob/master/Tools/Scripts/sort-Xcode-project-file)


### How to use
--------------
1. Put **xUnique.py** file in your project repository somewhere and add it as track file via `git add path/to/xUnique.py`, so all members could use the same script
2. create a git hook: `ln -s path/to/xUnique.py .git/hooks/pre-push`
3. Add permission `chmod 555 .git/hooks/pre-push` 
    * use hook `pre-push` instead of `pre-commit` is a safe consideration: you decide to commit the newly generated project file or not
4. In all your branches, uniquify `project.pbxproj` file in either way:
	* make some changes and commit. Try to push, git hook would be triggered
	* manually run script: `python path/to/xUnique.py path/to/MyProject.xcodeproj` and then committing changes.
5. All Done;) 

### NOTICE
----------
* All project members must add this `pre-push` hook. Thus the project file would be consistent in the repository.
* Tested supported `isa` types:
    * `PBXProject`
    * `XCConfigurationList`
    * `PBXNativeTarget`
    * `PBXTargetDependency`
    * `PBXContainerItemProxy`
    * `XCBuildConfiguration`
    * `PBXSourcesBuildPhase`
    * `PBXFrameworksBuildPhase`
    * `PBXResourcesBuildPhase`
    * `PBXBuildFile`
    * `PBXReferenceProxy`
    * `PBXFileReference`
    * `PBXGroup`
    * `PBXVariantGroup`


### Authors
-----------
* Xiao Wang ([seganw](http://fclef.wordpress.com/about))

### Contributions
-----------------
I only tested on one single project and one project with a subproject, so maybe there should be more unconsidered conditions. 
If you get any problem, feel free to fire a Pull Request or Issue


### License
-----------
Licensed under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.