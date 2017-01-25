C#-MSMQ

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Messaging;
using System.Diagnostics;

namespace # C-MSMQ
{
    class Program
    {
        static void Main(string[] args)
        {
            const string queueName = @".\private$\TestQueue";
            Stopwatch stopWatch = new Stopwatch();
            stopWatch.Start();
            FireCommand(queueName);
            stopWatch.Stop();
            TimeSpan TSP = stopWatch.Elapsed;
            Console.WriteLine("Average time for sending nonpersistent message is:{0} seconds.", (double)TSP.Seconds / 100.0);
            double test = (double)TSP.Seconds / 100.0; 
           
            stopWatch.Start();
            FireCommand2(queueName);
            stopWatch.Stop();
            TSP = stopWatch.Elapsed;
            Console.WriteLine("Average time for sending persistent message is:{0} seconds.", (double)TSP.Seconds / 100.0);
            double test1 = (double)TSP.Seconds / 100.0;
            double res = test1 - test;
            double res1 = (test1 / test)-1;
            string strRes = res1.ToString("0%");
            double res2 = (test / test1);
            string strRes1 = res2.ToString("0%");
            Console.WriteLine("Diference between messages is seconds {0}.", res);
            Console.WriteLine("Persistent message need {0} more time.", strRes);
            Console.WriteLine("Nonpersistent message use {0} time of persistent message.", strRes1);
            Console.ReadLine();
        }
        private static void FireCommand2(string queueName)
        {
            Console.WriteLine("Write 1 to send 100 persistent messages.");
            var command = Console.ReadLine();
            if (command.Equals("1"))
            {
                for (int i = 1; i < 101; i++)
                {
                    SendMessageToQueue2(queueName);
                }
            }
        }
        private static void FireCommand(string queueName)
        {
            Console.WriteLine("\nWrite 1 to send 100 nonpersistent messages.");
            var command = Console.ReadLine();
            if (command.Equals("1"))
            {
                for (int i = 1; i < 101; i++)
                {
                    SendMessageToQueue(queueName);
                }
            }
        }
        private static void ReceiveMessageFromQueue(string queueName)
        {
            MessageQueue msMq = msMq = new MessageQueue(queueName);
            try
            {
                msMq.Formatter = new XmlMessageFormatter(new Type[] { typeof(string) });
            }
            catch (MessageQueueException ee)
            {
                Console.Write(ee.ToString());
            }
            catch (Exception eee)
            {
                Console.Write(eee.ToString());
            }
            finally
            {
                msMq.Close();
            }
            Console.WriteLine("Message received ......");
        }
        private static void SendMessageToQueue(string queueName)
        {
            MessageQueue msMq = null;
            Stopwatch stopWatch = new Stopwatch();
            if (!MessageQueue.Exists(queueName))
            {
                msMq = MessageQueue.Create(queueName);
            }
            else
            {
                msMq = new MessageQueue(queueName);
            }
            try
            {
                string s = new string('A', 100000);
                msMq.Send(s);
            }
            catch (MessageQueueException ee)
            {
                Console.Write(ee.ToString());
            }
            catch (Exception eee)
            {
                Console.Write(eee.ToString());
            }
            finally
            {
                msMq.Close();
            }
        }
        private static void SendMessageToQueue2(string queueName)
        {
            string s = new string('A', 100000);
            Message recoverableMessage = new Message();
            recoverableMessage.Recoverable = true;
            recoverableMessage.Body = s;
            MessageQueue msMq = null;
            Stopwatch stopWatch = new Stopwatch();
            if (!MessageQueue.Exists(queueName))
            {
                msMq = MessageQueue.Create(queueName);
            }
            else
            {
                msMq = new MessageQueue(queueName);
            }
            try
            {
                stopWatch.Start();
                msMq.Send(recoverableMessage);

            }
            catch (MessageQueueException ee)
            {
                Console.Write(ee.ToString());
            }
            catch (Exception eee)
            {
                Console.Write(eee.ToString());
            }
            finally
            {
                msMq.Close();
            }
            Console.WriteLine("Message sent ......");
            stopWatch.Stop();
        }
    }
}

  
