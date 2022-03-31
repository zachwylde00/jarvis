using System;
using System.Diagnostics;
using jarvisWPF;
using System.Reflection;
using System.Speech.Recognition;
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using System.IO;

namespace LINKS
{

    //public class MyClass : jarvisWPF.Classes.Plugins.StaticIInterface
    //{
    //    public void OnLoad()
    //    {
    //        throw new NotImplementedException();
    //    }

    //    public void _SpeechRecognized(object sender, System.Speech.Recognition.SpeechRecognizedEventArgs e)
    //    {
    //        throw new NotImplementedException();
    //    }
    //}


    public static class LExecute
    {
        static string CurrentPluginDirectory;
        
        /// <summary>
        /// This method is run on load, so all the needed events are attached automatically.
        /// </summary>
        public static void OnLoad()
        {
            // Attach events when LINKS recognizes a phrase
            PublicClass.Recognizer.SpeechRecognized += _SpeechRecognized;

            // Create plugin directory
            CurrentPluginDirectory = PublicClass.PluginsPath + typeof(LExecute).Assembly.GetName().Name + "\\";            
            System.IO.Directory.CreateDirectory(CurrentPluginDirectory);
        }

        private static void _SpeechRecognized(object sender, SpeechRecognizedEventArgs e)
        {
            string RecognizedText = e.Result.Text;
            float RecognizedConfidence = e.Result.Confidence;

            WriteLogAsync(RecognizedText, e.Result.Confidence.ToString());
            //using (System.IO.StreamWriter file = new System.IO.StreamWriter(CurrentPluginDirectory + "LINKS_Heard.txt", true))
            //{
            //    string StrToWrite = string.Format("{2}\tRecognized@{0}: \t{1}", new object[] { RecognizedConfidence, RecognizedText, dt });
            //    file.WriteLine(StrToWrite);
            //}
        }

        private static async Task WriteLogAsync(string heardPhrase, string phraseConfidence)
        {
            DateTime dt = DateTime.Now;
            string filePath = CurrentPluginDirectory + "LINKS_Heard.txt";

            using (StreamWriter ErrorStreamWriter = new StreamWriter(new FileStream(filePath, FileMode.Append, FileAccess.Write, FileShare.Write)))
            {
                string StrToWrite = string.Format("{2}\tRecognized@{0}: \t{1}", new object[] { phraseConfidence, heardPhrase, dt });
                await ErrorStreamWriter.WriteLineAsync(StrToWrite);
            }
        }

        /// <summary>
        /// Calls a command from default commands
        /// </summary>
        /// <param name="command">All of the commands listed in default command tab</param>
        public static void CallDefaultCommand(string command)
        {
            //PublicClass.MainControlCenter.callToCaseStructure(command);
            //Thread t = new Thread(() => PublicClass.MainControlCenter.shellRecoThread(command, 1));
            //t.Start();

            //Task shellRecoTask = Task.Run(() => //unloads grammar without hanging the interface and without using threading
            //{
            //    PublicClass.MainControlCenter.shellRecoThread(command, 1);
            //});
            ////Task.WaitAll(rebuildGrammars);
            //Thread.Sleep(1);
            PublicClass.Plugins.CallAllCommands(command);
            //jarvisWPF.Classes.Plugins.PluginController.CallCommand(command);
        }

        //public static void RunAsShell(string shellCommand, string responseAfterExecution, string responseBeforeConfirmation, string responseFailed)
        //{
        //    PublicClass.MainControlCenter.RunShellCommand(shellCommand, responseAfterExecution, responseBeforeConfirmation, responseFailed);            
        //}
        
        /// <summary>
        /// Call from Jarvis commands or response:
        /// [LINKS.LExecute.ExecuteProcess(notepad,failed to load notepad;can't load notepad)]
        /// </summary>
        /// <param name="execPath">executable path</param>
        /// <param name="failedPrompt">string to speak on failed execution</param>
        /// <param name="successPrompt">string to speak after successful execution</param>
        public static void ExecuteProcess(string execPath, string failedPrompt, string successPrompt)
        {
            // This will run internal function and speak if failed.
            if (PublicClass.ExecuteProcess(execPath, "", false, ProcessWindowStyle.Normal) == "1")
            {
                SpeakFromLINKS(successPrompt);
            }
            else
            {
                SpeakFromLINKS(failedPrompt);
            }            
        }

        /// <summary>
        /// Speaks random string through LINKS
        /// </summary>
        /// <param name="stringToSpeak">Simple string or strings seperated by ;</param>
        public static string SpeakFromLINKS(string stringToSpeak)
        {
            string retVal = string.Empty;

            try
            {
                // Speak from Jarvis
                retVal = PublicClass.SpeechSynth.SpeakRandomPhrase(stringToSpeak);
            }
            catch { }
            finally
            {
                if (retVal == string.Empty | retVal.StartsWith("Error:"))
                {
                    retVal = "Error: SpeakFromLINKS commad failed.";
                }
            }

            return retVal;
        }

        /// <summary>
        /// Call from Jarvis commands or response:
        /// [LINKS.LExecute.SpeakTextFileFromLINKS(c:\temp\test.txt)]
        /// Speaks random string seperated ; through LINKS using text file contents
        /// </summary>
        /// <param name="TextFilePath">Text file path where string is stored to be spoken</param>
        public static string SpeakTextFileFromLINKS(string TextFilePath)
        {
            string retVal = string.Empty;
            try
            {
                // Speak from Jarvis            
                //retVal = SpeakFromLINKS(System.IO.File.ReadAllText(TextFilePath));
                retVal = System.IO.File.ReadAllText(TextFilePath);
                PublicClass.SpeechSynth.SpeakRandomPhrase(retVal);
            }
            catch { }
            finally
            {
                if (retVal == string.Empty | retVal.StartsWith("Error:"))
                {
                    retVal = "Error: SpeakTextFileFromLINKS commad failed.";
                }
            }

            return retVal;
        }

        /// <summary>
        /// Call from Jarvis commands or response:
        /// [LINKS.LExecute.ReturnNotepadPath]
        /// </summary>
        /// <returns>A simple string</returns>
        public static string ReturnNotepadPath()
        {
            // Speak from Jarvis
            PublicClass.SpeechSynth.SpeakRandomPhrase("adding notepad string in command");

            // Plays wav file from Sound Effects folder
            PublicClass.SoundEffectPlayer.PlaySound("system_ready", false);

            return "notepad";
        }

        /// <summary>
        /// Call from Jarvis commands or response:
        /// [LINKS.LExecute.Run(notepad,true]
        /// </summary>
        /// <param name="execPath">Executable path</param>
        /// <param name="WaitTillEnd">Waits till the command is executed and exited</param>
        /// <returns></returns>
        public static string Run(string execPath, string WaitTillEnd)
        {
            ProcessStartInfo startInfo = new ProcessStartInfo();
            startInfo.CreateNoWindow = true;
            startInfo.WindowStyle = ProcessWindowStyle.Normal;
            startInfo.Arguments = "";
            startInfo.FileName = execPath;
            startInfo.UseShellExecute = true;

            try
            {
                // Start the process with the info we specified.
                // Call WaitForExit and then the using statement will close.
                using (Process exeProcess = Process.Start(startInfo))
                {
                    if (WaitTillEnd.ToLower() == "true")
                    {
                        //Wait till process is exited
                        exeProcess.WaitForExit();
                    }
                }
            }

            catch (Exception error)
            {
                //Write error.Message to file or speak if needed
                //Debugger.Break();
                Console.WriteLine(error.Message);
            }

            return "";
        }

        public static object ProcessCmd(string AssemblyName, string className, string methodName, object[] parameterForTheMethod)

        {
            object returnObject = null;
            MethodInfo mi = null;
            ConstructorInfo ci = null;
            object responder = null;
            Type type = null;
            System.Type[] objectTypes;
            int count = 0;
            try
            {
                //Load the assembly and get it's information
                type = System.Reflection.Assembly.LoadFrom(AssemblyName +   ".dll").GetType(AssemblyName +"." + className);
                //Get the Passed parameter types to find the method type
                objectTypes = new System.Type[parameterForTheMethod.GetUpperBound(0) + 1];
                foreach (object objectParameter in parameterForTheMethod)

                {
                    if (objectParameter != null)
                        objectTypes[count] = objectParameter.GetType();
                    count++;
                }

                //Get the reference of the method
                mi = type.GetMethod(methodName, objectTypes);
                ci = type.GetConstructor(Type.EmptyTypes);
                responder = ci.Invoke(null);
                //Invoke the method
                returnObject = mi.Invoke(responder, parameterForTheMethod);
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {
                mi = null;
                ci = null;
                responder = null;
                type = null;
                objectTypes = null;
            }

            //Return the value as a generic object
            return returnObject;

        }
        
    }
}
