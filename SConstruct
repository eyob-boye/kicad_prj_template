#-----------------------------------------------------------------------------
#  Configure for this project
#-----------------------------------------------------------------------------
PROJECT_NAME = "kicad_prj_template"

IMPORTED_KICADLIBS = [
("#imported/kicadlib_macrofab", 'https://bitbucket.org/eyob/kicadlib_macrofab.git', "v0.2")
]


#-----------------------------------------------------------------------------
# Target environment.
#-----------------------------------------------------------------------------
import os, urlparse
EnsurePythonVersion(2,7)
EnsureSConsVersion(2,0,0)

trgt_env = Environment(tools = ['Git', 'kicad_project', 'kicad_gerbers', 'kicad_xyrs'])

#-----------------------------------------------------------------------------
# Iterate through each dependency
#-----------------------------------------------------------------------------
result = {"pretty":[], "lib":[]}
for (kicadlib_dst, kicadlib_url, kicadlib_ver) in IMPORTED_KICADLIBS:
    # Check if the url
    kicadlib_url_path = urlparse.urlparse(kicadlib_url)
    if len(kicadlib_url_path[0]) > 1:
        if "file://" in kicadlib_url:
            kicadlib_dst = kicadlib_url
        else:
            # Get from remote location
            branch_str = ""
            if(kicadlib_ver.strip()):
                branch_str = ' --branch=%s' % kicadlib_ver.strip()
            trgt_env.GetComponentFromGit(kicadlib_dst, kicadlib_url + branch_str)
            kicadlib_dst = trgt_env.Dir(kicadlib_dst).abspath
    else:
        kicadlib_dst = kicadlib_url

    # Visit the destination and do a recursive search for .lib and .pretty
    result['lib'] += trgt_env.FindComponentFiles(['lib'], exclude=[], search_root=kicadlib_dst, recursive=True)
    result['pretty'] += trgt_env.FindComponentFiles(['pretty'], exclude=[], search_root=kicadlib_dst, recursive=True)

#-----------------------------------------------------------------------------
# Build the kicad project files
#-----------------------------------------------------------------------------
kicad_prj = trgt_env.KiCadProject(PROJECT_NAME+".pro" , result["lib"] + result["pretty"])
# WARNING: If the .pro and .sch files are not precious and noclean then you might
# lose your files. So don't change the following lines without understing the consequences.
trgt_env.Precious(kicad_prj)
trgt_env.NoClean(kicad_prj)
trgt_env.AlwaysBuild(kicad_prj)
# Make this the default target, all others such as gerbers need to be excplicty built
trgt_env.Default(kicad_prj)


#-----------------------------------------------------------------------------
# Build the kicad project files
#-----------------------------------------------------------------------------
#trgt_env.Execute("@$KICAD_PYTHONCOM --version")
kicad_gerbers = trgt_env.KiCadGerber(Dir("gerbers"), PROJECT_NAME+".kicad_pcb")
trgt_env.Alias("gerbers", kicad_gerbers)


#-----------------------------------------------------------------------------
# Build the kicad xyrs file
#-----------------------------------------------------------------------------
kicad_xyrs = trgt_env.KiCadXYRS("./bom/"+PROJECT_NAME+".xyrs", PROJECT_NAME+".kicad_pcb")
trgt_env.Alias("xyrs", kicad_xyrs)


#-----------------------------------------------------------------------------
# Build all
#-----------------------------------------------------------------------------
trgt_env.Alias("all", ["gerbers", "xyrs"])
