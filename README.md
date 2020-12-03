using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace PracticeFileStream
{
    class Program
    {
      static  Byte[] encodedBytes;
        static void Main(string[] args)
        {
            long offset1 = 0;
            int READSIZE1 = 5000000;
            long offset2 = 0;
            int READSIZE2 = 5000000;

            int count = 1;
            int count2 = 1;
            
            string pathFile = @"C:\Users\avoloxstation2\source\repos\PracticeFileStream\PracticeFileStream\Data\file1temp.txt";
            string pathFile2 = @"C:\Users\avoloxstation2\source\repos\PracticeFileStream\PracticeFileStream\Data\file2.txt";
           


        
            FileInfo fi = new FileInfo(pathFile);
            FileInfo fi2 = new FileInfo(pathFile2);
            long fileLength = fi.Length;
            


            // file2 = new FileStream(pathFile2,FileMode.Open);


            //Read from File1 and Write to File2
            using (FileStream fs = new FileStream(pathFile, FileMode.Open))
            {
                while (true)
                {
                    if (offset1 < fileLength)
                    {
                        byte[] buffer;
                        if (fileLength < READSIZE1)
                        {

                             buffer = new byte[fileLength];
                            fs.Read(buffer, 0, Convert.ToInt32(fileLength));

                        }
                        else
                        {
                             buffer = new byte[READSIZE1];
                            fs.Read(buffer, 0, READSIZE1);
                        }

                        MemoryStream  ms = new MemoryStream(buffer);
                        StreamReader sr = new StreamReader(ms);
                      
                        string currentData = sr.ReadToEnd();
                        string encodedCurrentData = Encoding.UTF8.GetString(Encoding.Default.GetBytes(currentData));

                        File.AppendAllText(pathFile2, encodedCurrentData, Encoding.UTF8);
                        ms.Dispose();
                        sr.Close();
                        offset1 = (count * READSIZE1) + 1;
                        count++;


                    }
                    else if(offset1>=fileLength)
                    {
                        Console.WriteLine("Copied from file1 to file 2");
                     

                        break;
                    }
                    
                }

            }

            long file2Length = fi2.Length;
            File.Create(pathFile).Close();

            using (FileStream fs = new FileStream(pathFile2, FileMode.Open))
            {
                while (true)
                {
                    if (offset2 < file2Length)
                    {
                        byte[] buffer;
                        if (file2Length < READSIZE2)
                        {

                            buffer = new byte[file2Length];
                            fs.Read(buffer, 0, Convert.ToInt32(file2Length));

                        }
                        else
                        {
                            buffer = new byte[READSIZE2];
                            fs.Read(buffer, 0, READSIZE2);
                        }


                        MemoryStream ms = new MemoryStream(buffer);
                        StreamReader sr = new StreamReader(ms);


                        string currentData = sr.ReadToEnd();

                    
                        File.AppendAllText(pathFile, currentData, Encoding.UTF8);
                        ms.Dispose();
                        sr.Close();
                        offset2 = (count2 * READSIZE2) + 1;
                        count2++;
                    }

                    else if (offset2 >= file2Length)
                    {
                        Console.WriteLine("Copied from file2 to file 1");
                        break;
                    }


                    }
            }

            File.Delete(pathFile2);
            Console.ReadLine();
        }

       
    }
}
