import React, { useState, useMemo, useRef, useEffect } from "react";
import { motion } from "framer-motion";
import { FaWhatsapp, FaArrowUp } from "react-icons/fa";

export default function EscortAgencyTemplate() {
  const listEndRef = useRef(null);
  const [showTop, setShowTop] = useState(false);

  // generate 20 sample profiles
  const SAMPLE_PROFILES = Array.from({ length: 20 }).map((_, i) => ({
    id: `p${i + 1}`,
    name: [
      "Ava",
      "Lina",
      "Maya",
      "Zuri",
      "Nia",
      "Sasha",
      "Talia",
      "Imani",
      "Chloe",
      "Bella",
      "Serena",
      "Amara",
      "Luna",
      "Keira",
      "Alina",
      "Jade",
      "Nora",
      "Yara",
      "Kira",
      "Elena",
    ][i],
    age: 22 + (i % 10),
    location: ["Nairobi", "Mombasa", "Nakuru"][i % 3],
    rate: 15000 + i * 1000,
    whatsapp: `+2547${Math.floor(10000000 + Math.random() * 89999999)}`,
    services: ["Dinner Companion", "Event Dates", "Private Meetups", "Weekend Getaways"].slice(0, 2 + (i % 2)),
    img: `https://source.unsplash.com/random/800x800/?woman,portrait&sig=${i}`,
    bio: "Elegant, professional, and here to make your experience memorable.",
  }));

  const [profiles] = useState(SAMPLE_PROFILES);
  const [query, setQuery] = useState("");
  const [location, setLocation] = useState("");
  const [minRate, setMinRate] = useState(0);
  const [maxRate, setMaxRate] = useState(100000);
  const [ageVerified, setAgeVerified] = useState(false);
  const [selected, setSelected] = useState(null);
  const [showBooking, setShowBooking] = useState(false);
  const [booking, setBooking] = useState({ name: "", email: "", date: "", message: "" });
  const [bookErrors, setBookErrors] = useState({});
  const [subscribed, setSubscribed] = useState(false);
  const [visibleCount, setVisibleCount] = useState(6);

  const filtered = useMemo(() => {
    const q = query.trim().toLowerCase();
    return profiles.filter((p) => {
      if (location && p.location.toLowerCase() !== location.toLowerCase()) return false;
      if (p.rate < minRate || p.rate > maxRate) return false;
      if (q) {
        const hay = `${p.name} ${p.bio} ${p.services.join(" ")}`.toLowerCase();
        if (!hay.includes(q)) return false;
      }
      return true;
    });
  }, [profiles, query, location, minRate, maxRate]);

  // smooth scroll to newly loaded profiles when visibleCount changes
  useEffect(() => {
    if (listEndRef.current) {
      listEndRef.current.scrollIntoView({ behavior: "smooth" });
    }
  }, [visibleCount]);

  // show "Back to Top" after scrolling down
  useEffect(() => {
    const handleScroll = () => setShowTop(window.scrollY > 300);
    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  }, []);

  function openProfile(p) {
    setSelected(p);
  }

  function openBooking(p) {
    if (!ageVerified) {
      window.scrollTo({ top: 0, behavior: "smooth" });
      setBookErrors({ general: "Please confirm you are 18 or older before booking." });
      return;
    }
    setSelected(p);
    setShowBooking(true);
    setBookErrors({});
    setBooking({ name: "", email: "", date: "", message: "" });
  }

  function validateBooking() {
    const errs = {};
    if (!booking.name.trim()) errs.name = "Name is required";
    if (!booking.email.includes("@")) errs.email = "Valid email required";
    if (!booking.date) errs.date = "Select a date";
    return errs;
  }

  function submitBooking(e) {
    e.preventDefault();
    const errs = validateBooking();
    setBookErrors(errs);
    if (Object.keys(errs).length) return;
    setShowBooking(false);
    // In a real app you'd post this to an API
    alert(`Booking request sent to ${selected?.name || "the companion"}. We'll contact you at ${booking.email}.`);
  }

  const handleLoadMore = () => setVisibleCount((prev) => Math.min(filtered.length, prev + 6));

  return (
    <div className="min-h-screen bg-gradient-to-br from-pink-50 via-purple-50 to-indigo-50 text-gray-900 relative">
      {/* Hero */}
      <section
        className="relative h-screen flex flex-col justify-center items-center bg-cover bg-center text-white"
        style={{
          backgroundImage:
            "url('https://images.unsplash.com/photo-1534081333815-ae5019106622?auto=format&fit=crop&w=1600&q=80')",
        }}
      >
        <div className="absolute inset-0 bg-black/55"></div>
        <motion.div
          initial={{ opacity: 0, y: 24 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.9 }}
          className="relative z-10 text-center px-6"
        >
          <h1 className="text-4xl md:text-6xl font-extrabold mb-4 drop-shadow-2xl">Experience Luxury &amp; Elegance</h1>
          <p className="max-w-2xl mx-auto text-lg md:text-xl opacity-90 mb-6">
            Kenya's premium companion service — where sophistication meets discretion.
          </p>
          <motion.button
            whileHover={{ scale: 1.06 }}
            className="px-8 py-3 bg-gradient-to-r from-yellow-400 to-pink-400 text-gray-900 rounded-full font-semibold shadow-2xl"
            onClick={() => window.scrollTo({ top: document.body.scrollHeight / 3, behavior: "smooth" })}
          >
            Explore Companions
          </motion.button>
        </motion.div>
      </section>

      {/* Sticky Header */}
      <header className="bg-gradient-to-r from-pink-500 to-purple-600 shadow-lg text-white sticky top-0 z-30">
        <div className="max-w-6xl mx-auto px-6 py-4 flex items-center justify-between">
          <div className="flex items-center gap-4">
            <div className="w-10 h-10 rounded-full bg-gradient-to-br from-yellow-300 to-pink-500 flex items-center justify-center text-white font-bold shadow">EA</div>
            <div>
              <div className="text-xl font-bold tracking-wide">Elyse — Escort Agency</div>
              <div className="text-sm opacity-80">Discreet • Professional • Verified</div>
            </div>
          </div>

          <div className="flex items-center gap-4">
            <label className="flex items-center gap-2 text-sm">
              <input
                type="checkbox"
                checked={ageVerified}
                onChange={(e) => setAgeVerified(e.target.checked)}
                className="rounded border-pink-300"
              />
              I am 18+
            </label>
            <a href="#contact" className="px-4 py-2 bg-yellow-400 text-gray-900 rounded-md text-sm font-medium shadow hover:bg-yellow-300 transition">
              Contact
            </a>
          </div>
        </div>
      </header>

      <main className="max-w-6xl mx-auto px-6 py-10">
        {/* Search & Filters */}
        <section className="grid md:grid-cols-3 gap-6 mb-8">
          <div className="md:col-span-2">
            <label className="block text-xs font-medium text-gray-700">Search</label>
            <input
              value={query}
              onChange={(e) => setQuery(e.target.value)}
              placeholder="Search by name, service, or bio"
              className="mt-1 block w-full rounded-lg border-pink-200 shadow focus:border-pink-400 focus:ring focus:ring-pink-100 p-3 bg-white"
            />
          </div>

          <div className="space-y-3">
            <div>
              <label className="block text-xs font-medium text-gray-700">Location</label>
              <select
                value={location}
                onChange={(e) => setLocation(e.target.value)}
                className="mt-1 block w-full rounded-lg border-pink-200 p-2 bg-white"
              >
                <option value="">Anywhere</option>
                <option value="Nairobi">Nairobi</option>
                <option value="Mombasa">Mombasa</option>
                <option value="Nakuru">Nakuru</option>
              </select>
            </div>

            <div>
              <label className="block text-xs font-medium text-gray-700">Rate range (KES)</label>
              <div className="flex gap-2 mt-1">
                <input
                  type="number"
                  value={minRate}
                  onChange={(e) => setMinRate(Number(e.target.value))}
                  className="w-1/2 rounded-lg border-pink-200 p-2 bg-white"
                />
                <input
                  type="number"
                  value={maxRate}
                  onChange={(e) => setMaxRate(Number(e.target.value))}
                  className="w-1/2 rounded-lg border-pink-200 p-2 bg-white"
                />
              </div>
            </div>
          </div>
        </section>

        {/* Profiles Grid */}
        <section>
          <div className="flex items-center justify-between mb-4">
            <h2 className="text-xl font-bold text-purple-700">Available Companions</h2>
            <p className="text-sm text-gray-600">
              Showing {Math.min(visibleCount, filtered.length)} of {filtered.length}
            </p>
          </div>

          <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
            {filtered.slice(0, visibleCount).map((p) => (
              <motion.article
                key={p.id}
                whileHover={{ scale: 1.03, boxShadow: "0px 12px 30px rgba(0,0,0,0.12)" }}
                transition={{ type: "spring", stiffness: 220 }}
                className="bg-white rounded-2xl shadow-lg overflow-hidden border border-pink-100 cursor-pointer"
              >
                <motion.img
                  src={p.img}
                  alt={p.name}
                  className="w-full h-44 object-cover"
                  whileHover={{ scale: 1.05 }}
                  transition={{ duration: 0.35 }}
                />

                <div className="p-4">
                  <div className="flex items-start justify-between">
                    <div>
                      <h3 className="text-lg font-bold text-pink-700">
                        {p.name}, <span className="font-normal text-sm text-gray-500">{p.age}</span>
                      </h3>
                      <p className="text-sm text-purple-500">{p.location}</p>
                    </div>

                    <div className="text-right">
                      <div className="text-xs text-gray-500">From</div>
                      <div className="text-xl font-semibold text-indigo-600">KES {p.rate.toLocaleString()}</div>
                    </div>
                  </div>

                  <p className="mt-3 text-sm text-gray-700 line-clamp-3">{p.bio}</p>

                  <div className="mt-3 flex items-center gap-2">
                    <a
                      href={`https://wa.me/${p.whatsapp.replace(/[^\d]/g, "")}`}
                      target="_blank"
                      rel="noopener noreferrer"
                      className="flex items-center gap-2 text-green-600 hover:underline text-sm"
                    >
                      <FaWhatsapp /> {p.whatsapp}
                    </a>
                  </div>

                  <div className="mt-4 flex items-center justify-between gap-2">
                    <div className="flex gap-2 flex-wrap">
                      {p.services.map((s) => (
                        <motion.span
                          key={s}
                          className="text-xs px-2 py-1 rounded-full bg-pink-50 text-pink-700 border border-pink-200"
                          whileHover={{ scale: 1.06 }}
                        >
                          {s}
                        </motion.span>
                      ))}
                    </div>

                    <div className="flex items-center gap-2">
                      <motion.button whileHover={{ scale: 1.06 }} onClick={() => openProfile(p)} className="px-3 py-1 rounded-md border border-pink-200 text-sm hover:bg-pink-50">
                        View
                      </motion.button>
                      <motion.button whileHover={{ scale: 1.06 }} onClick={() => openBooking(p)} className="px-3 py-1 rounded-md bg-gradient-to-r from-pink-500 to-purple-600 text-white text-sm shadow">
                        Book
                      </motion.button>
                    </div>
                  </div>
                </div>
              </motion.article>
            ))}

            {/* anchor used for smooth scroll when loading more */}
            <div ref={listEndRef} />
          </div>

          {visibleCount < filtered.length && (
            <div className="flex justify-center mt-6">
              <motion.button
                whileHover={{ scale: 1.05 }}
                className="px-6 py-2 rounded-md bg-gradient-to-r from-pink-500 to-purple-600 text-white shadow"
                onClick={handleLoadMore}
              >
                Load More
              </motion.button>
            </div>
          )}
        </section>

        {/* Booking Modal */}
        {showBooking && selected && (
          <div className="fixed inset-0 z-50 flex items-center justify-center px-4">
            <div className="fixed inset-0 bg-black/50" onClick={() => setShowBooking(false)} />
            <motion.form
              onSubmit={submitBooking}
              initial={{ opacity: 0, y: 20 }}
              animate={{ opacity: 1, y: 0 }}
              transition={{ duration: 0.25 }}
              className="relative bg-white rounded-xl shadow-2xl max-w-xl w-full p-6 z-10 border border-pink-100"
            >
              <h3 className="text-lg font-bold text-purple-700">Book {selected.name}</h3>
              {bookErrors.general && <p className="mt-2 text-sm text-red-600">{bookErrors.general}</p>}

              <div className="grid grid-cols-1 sm:grid-cols-2 gap-3 mt-4">
                <div>
                  <label className="text-xs font-medium text-gray-700">Your name</label>
                  <input value={booking.name} onChange={(e) => setBooking({ ...booking, name: e.target.value })} className="mt-1 block w-full rounded-md border-pink-200 p-2 bg-pink-50" />
                  {bookErrors.name && <p className="text-xs text-red-600">{bookErrors.name}</p>}
                </div>
                <div>
                  <label className="text-xs font-medium text-gray-700">Email</label>
                  <input value={booking.email} onChange={(e) => setBooking({ ...booking, email: e.target.value })} className="mt-1 block w-full rounded-md border-pink-200 p-2 bg-pink-50" />
                  {bookErrors.email && <p className="text-xs text-red-600">{bookErrors.email}</p>}
                </div>
                <div>
                  <label className="text-xs font-medium text-gray-700">Date &amp; time</label>
                  <input type="datetime-local" value={booking.date} onChange={(e) => setBooking({ ...booking, date: e.target.value })} className="mt-1 block w-full rounded-md border-pink-200 p-2 bg-pink-50" />
                  {bookErrors.date && <p className="text-xs text-red-600">{bookErrors.date}</p>}
                </div>
                <div>
                  <label className="text-xs font-medium text-gray-700">Message</label>
                  <input value={booking.message} onChange={(e) => setBooking({ ...booking, message: e.target.value })} className="mt-1 block w-full rounded-md border-pink-200 p-2 bg-pink-50" />
                </div>
              </div>

              <div className="mt-4 flex items-center justify-between">
                <div className="flex items-center gap-2">
                  <input type="checkbox" id="terms" className="rounded border-pink-300" required />
                  <label htmlFor="terms" className="text-sm text-gray-500">I agree to the terms and confirm bookings are for lawful, consensual services.</label>
                </div>
                <div className="flex gap-2">
                  <button type="button" onClick={() => setShowBooking(false)} className="px-3 py-1 rounded-md border border-pink-200">Cancel</button>
                  <button type="submit" className="px-4 py-1 rounded-md bg-gradient-to-r from-pink-500 to-purple-600 text-white">Send Request</button>
                </div>
              </div>
            </motion.form>
          </div>
        )}

        {/* Selected profile drawer */}
        {selected && (
          <div className="fixed right-4 bottom-4 z-40 max-w-md w-full">
            <motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.2 }} className="bg-white rounded-xl shadow-2xl p-4 ring-1 ring-pink-50">
              <div className="flex items-center gap-3">
                <img src={selected.img} alt={selected.name} className="w-16 h-16 rounded-lg object-cover" />
                <div className="flex-1">
                  <div className="flex items-center justify-between">
                    <div>
                      <h4 className="font-semibold text-pink-700">{selected.name} <span className="text-sm text-gray-500">{selected.age}</span></h4>
                      <div className="text-sm text-purple-500">{selected.location}</div>
                    </div>
                    <div className="text-right">
                      <div className="text-sm">KES {selected.rate.toLocaleString()}</div>
                      <div className="text-xs text-gray-400">per hour</div>
                    </div>
                  </div>

                  <p className="mt-2 text-sm text-gray-700">{selected.bio}</p>

                  <div className="mt-3 flex gap-2">
                    <button className="px-3 py-1 rounded-md bg-gradient-to-r from-pink-500 to-purple-600 text-white text-sm" onClick={() => openBooking(selected)}>Book</button>
                    <button className="px-3 py-1 rounded-md border" onClick={() => setSelected(null)}>Close</button>
                  </div>
                </div>
              </div>
            </motion.div>
          </div>
        )}

        {/* Footer / Newsletter */}
        <footer className="mt-12 border-t pt-6" id="contact">
          <div className="flex flex-col sm:flex-row items-center justify-between gap-4">
            <div>
              <h4 className="font-semibold text-purple-700">Stay informed</h4>
              <p className="text-sm text-gray-600">Subscribe for promotions and new profiles — we protect your privacy.</p>
            </div>
            <form
              onSubmit={(e) => {
                e.preventDefault();
                setSubscribed(true);
                alert("Subscribed!");
              }}
              className="flex gap-2 w-full sm:w-auto"
            >
              <input type="email" placeholder="Your email" required className="rounded-md border-pink-200 p-2 bg-pink-50" />
              <motion.button whileHover={{ scale: 1.06 }} className="px-3 py-2 rounded-md bg-gradient-to-r from-pink-500 to-purple-600 text-white">
                Subscribe
              </motion.button>
            </form>
          </div>

          <div className="mt-6 text-xs text-gray-500">&copy; {new Date().getFullYear()} Elyse Agency — Discreet service. All bookings subject to local laws and consent.</div>
        </footer>
      </main>

      {/* Floating WhatsApp Button */}
      <a
        href="https://wa.me/254700000000?text=Hello%2C%20I%20have%20a%20general%20inquiry%20about%20your%20services."
        target="_blank"
        rel="noopener noreferrer"
        className="fixed bottom-6 right-6 bg-green-500 hover:bg-green-600 text-white rounded-full p-4 shadow-lg flex items-center justify-center"
        aria-label="WhatsApp contact"
      >
        <FaWhatsapp size={28} />
      </a>

      {/* Back to Top Button */}
      {showTop && (
        <motion.button
          onClick={() => window.scrollTo({ top: 0, behavior: "smooth" })}
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          className="fixed bottom-20 right-6 bg-purple-600 hover:bg-purple-700 text-white p-3 rounded-full shadow-lg"
          aria-label="Back to top"
        >
          <FaArrowUp />
        </motion.button>
      )}
    </div>
  );
}
